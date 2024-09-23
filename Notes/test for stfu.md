Apologies for the inconvenience and confusion caused by the errors in the previous function implementation. Let's address the issues systematically to ensure your `automated_regression_analysis` function operates smoothly and efficiently.

## **1. Understanding the Error**

### **Error Message:**
```
Error in diagnostics$bptest$p.value : 
  $ operator is invalid for atomic vectors
```

### **Cause of the Error:**
This error occurs because the function attempts to access the `p.value` component of `diagnostics$bptest`, assuming it is a list containing a `p.value` field. However, due to an unsuccessful attempt to perform the Breusch-Pagan test (`bptest`), `diagnostics$bptest` may end up being an atomic vector (like `NA`) instead of a list. Consequently, accessing `diagnostics$bptest$p.value` becomes invalid.

### **Impact:**
- **Function Termination:** The function halts execution upon encountering this error, preventing subsequent plots and analyses from running.
- **Incomplete Outputs:** Valuable diagnostic plots and results are not generated, interrupting your workflow.

## **2. Correcting the Function**

To resolve the error and enhance the functionality of your `automated_regression_analysis` function, we'll implement the following corrections and improvements:

1. **Robust Diagnostic Handling:**
   - Ensure that diagnostic tests always return a list with a `p.value` component, even if the test fails. This prevents the `$` operator error.
   
2. **Enhanced Plotting:**
   - Arrange multiple diagnostic plots on the same page, displaying four plots per page for better visualization and organization.
   
3. **Improved Interaction Term Handling:**
   - Automatically include interaction terms based on high correlation (e.g., correlation coefficient > 0.7) among predictors to capture significant combined effects without overcomplicating the model.
   
4. **Safe Model Selection:**
   - Implement safeguards to handle cases where no models pass all diagnostic checks, ensuring that the function selects the best possible model without causing errors.
   
5. **Clear Function Structure and Comments:**
   - Add comprehensive comments and structure the function for better readability and maintenance.

### **Corrected `automated_regression_analysis` Function**

Below is the **corrected and enhanced** version of your `automated_regression_analysis` function. Replace your existing function with this one to eliminate the error and benefit from the improved features.

```r
# Define the Enhanced Automated Regression Analysis Function
automated_regression_analysis <- function(data, formula, time_series = FALSE) {
  
  # Extract Response and Predictor Names
  response <- all.vars(formula)[1]
  predictors <- all.vars(formula)[-1]
  
  # Automatically Detect and Create Interaction Terms Based on Correlation
  # Interactions are created only for highly correlated predictors (correlation > 0.7)
  if(length(predictors) > 1){
    predictor_combinations <- combn(predictors, 2, simplify = FALSE)
    high_corr_interactions <- sapply(predictor_combinations, function(pair) {
      cor_val <- cor(data[[pair[1]]], data[[pair[2]]], use = "complete.obs")
      if(abs(cor_val) > 0.7) {
        return(paste(pair, collapse = ":"))
      } else {
        return(NULL)
      }
    })
    high_corr_interactions <- high_corr_interactions[!sapply(high_corr_interactions, is.null)]
  } else {
    high_corr_interactions <- character(0)
  }
  
  # Create a List of Predefined Alternative Models with Interaction Terms
  predefined_alternatives <- list(
    "Linear" = formula,
    "Log-Linear" = as.formula(paste("log(", response, ") ~ ", paste(predictors, collapse = " + "), sep = "")),
    "Log-Log" = as.formula(paste("log(", response, ") ~ ", paste(paste0("log(", predictors, ")"), collapse = " + "), sep = "")),
    "Quadratic" = as.formula(
      paste(
        response, "~", 
        paste(predictors, collapse = " + "), 
        "+", 
        paste(paste0("I(", predictors, "^2)"), collapse = " + ")
      )
    ),
    "Cubic" = as.formula(
      paste(
        response, "~", 
        paste(predictors, collapse = " + "), 
        "+", 
        paste(paste0("I(", predictors, "^3)"), collapse = " + ")
      )
    )
  )
  
  # Add Interaction Models if High Correlation Exists
  if(length(high_corr_interactions) > 0){
    interaction_formula <- as.formula(
      paste(response, "~", paste(c(predictors, high_corr_interactions), collapse = " + "))
    )
    predefined_alternatives[["Interactions"]] <- interaction_formula
  }
  
  # Initialize Storage for Models and Their Diagnostics
  model_summaries <- list()
  diagnostic_results <- list()
  model_performance <- data.frame(Model = character(),
                                  Adjusted_R2 = numeric(),
                                  AIC = numeric(),
                                  BIC = numeric(),
                                  stringsAsFactors = FALSE)
  
  # Define a Helper Function to Perform Diagnostics
  perform_diagnostics <- function(lm_model, time_series_flag) {
    diagnostics <- list()
    
    # Shapiro-Wilk Test for Normality of Residuals
    diagnostics$shapiro <- tryCatch({
      shapiro.test(lm_model$residuals)
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # Breusch-Pagan Test for Heteroskedasticity
    diagnostics$bptest <- tryCatch({
      bptest(lm_model)
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # RESET Test for Functional Form
    diagnostics$reset <- tryCatch({
      resettest(lm_model, power = 2:3, type = "fitted")
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # Variance Inflation Factor (VIF) for Multicollinearity (if multiple predictors)
    if(length(coef(lm_model)) > 2) {  # More than one predictor
      diagnostics$vif <- tryCatch({
        vif(lm_model)
      }, error = function(e) {
        NA
      })
    } else {
      diagnostics$vif <- NA
    }
    
    # Breusch-Godfrey Test for Autocorrelation (if time_series = TRUE)
    if(time_series_flag) {
      diagnostics$bgtest <- tryCatch({
        bgtest(lm_model)
      }, error = function(e) {
        list(p.value = NA)
      })
    } else {
      diagnostics$bgtest <- NA
    }
    
    return(diagnostics)
  }
  
  # Define a Helper Function to Correct for Heteroskedasticity and Autocorrelation
  correct_model <- function(lm_model, diagnostics, time_series_flag, data, formula_used) {
    corrected_model <- NULL
    correction_steps <- c()
    
    # Correct for Heteroskedasticity using GLS with variance structures
    if(!is.na(diagnostics$bptest$p.value) && diagnostics$bptest$p.value < 0.05){
      cat("Heteroskedasticity detected. Applying GLS with varPower.\n")
      corrected_model <- tryCatch({
        gls(formula_used, data = data, weights = varPower())
      }, error = function(e) {
        cat("Failed to apply varPower for heteroskedasticity correction.\n")
        NULL
      })
      if(!is.null(corrected_model)){
        correction_steps <- c(correction_steps, "Heteroskedasticity corrected with varPower")
      }
    }
    
    # Correct for Autocorrelation using GLS with AR(1) structure
    if(time_series_flag && !is.na(diagnostics$bgtest$p.value) && diagnostics$bgtest$p.value < 0.05){
      cat("Autocorrelation detected. Applying GLS with AR(1) correlation structure.\n")
      if(is.null(corrected_model)){
        corrected_model <- tryCatch({
          gls(formula_used, data = data, correlation = corAR1())
        }, error = function(e) {
          cat("Failed to apply AR(1) for autocorrelation correction.\n")
          NULL
        })
      } else {
        corrected_model <- tryCatch({
          update(corrected_model, correlation = corAR1())
        }, error = function(e) {
          cat("Failed to update GLS model with AR(1) correlation structure.\n")
          NULL
        })
      }
      if(!is.null(corrected_model)){
        correction_steps <- c(correction_steps, "Autocorrelation corrected with AR(1)")
      }
    }
    
    # Return the Corrected Model and Steps Taken
    return(list(model = corrected_model, steps = correction_steps))
  }
  
  # Function to plot diagnostics
  plot_diagnostics <- function(lm_model, model_type, time_series_flag) {
    # Set up plot area for 4 plots per page
    par(mfrow=c(2,2))
    
    # Standard Diagnostic Plots
    plot(lm_model, main = paste("Diagnostic Plots -", model_type))
    
    # Residuals over Time (if applicable)
    if(time_series_flag && "Time" %in% names(data)){
      plot(resid(lm_model) ~ data$Time, type = 'l', main = paste("Residuals over Time -", model_type),
           xlab = "Time", ylab = "Residuals")
      abline(h=0, lty = 2, col = "red")
    }
    
    # Fitted vs Actual Values
    plot(fitted(lm_model), data[[response]], main = paste("Fitted vs Actual -", model_type),
         xlab = "Fitted Values", ylab = "Actual Values")
    abline(0,1, col = "blue", lty = 2)
    lines(fitted(lm_model), fitted(lm_model), col = "green")
    
    # Partial Residual Plots
    if(length(coef(lm_model)) > 2){
      crPlots(lm_model, main = paste("Component + Residual Plots -", model_type))
    }
    
    # Reset plot layout
    par(mfrow=c(1,1))
  }
  
  # Fit All Predefined Alternative Models and Collect Diagnostics
  for(model_name in names(predefined_alternatives)) {
    cat("\n=== Fitting Model:", model_name, "===\n")
    current_formula <- predefined_alternatives[[model_name]]
    
    # Fit the Model
    lm_model <- tryCatch(lm(current_formula, data = data), silent = TRUE)
    
    if(class(lm_model) == "try-error") {
      cat("Failed to fit model:", model_name, "\n")
      next  # Skip to next model if fitting fails
    }
    
    cat("\n")
    print(summary(lm_model))
    
    # Perform Diagnostics
    diagnostics <- perform_diagnostics(lm_model, time_series)
    diagnostic_results[[model_name]] <- diagnostics
    
    # Correct the Model if Necessary
    corrections <- correct_model(lm_model, diagnostics, time_series, data, current_formula)
    if(!is.null(corrections$model)){
      lm_model_corrected <- corrections$model
      cat("Corrections Applied:", paste(corrections$steps, collapse = "; "), "\n")
      print(summary(lm_model_corrected))
      # Update diagnostics after correction
      diagnostics_corrected <- perform_diagnostics(lm_model_corrected, time_series)
      diagnostic_results[[model_name]] <- diagnostics_corrected
      # Replace the model with the corrected one
      lm_model <- lm_model_corrected
    }
    
    # Plot diagnostics
    plot_diagnostics(lm_model, model_name, time_series)
    
    # Store Model Summary
    model_summaries[[model_name]] <- summary(lm_model)
    
    # Calculate Model Performance Metrics
    adj_r2 <- summary(lm_model)$adj.r.squared
    model_aic <- AIC(lm_model)
    model_bic <- BIC(lm_model)
    
    # Ensure all performance metrics have length 1
    # Append only if all metrics are non-NA and single
    if(length(adj_r2) == 1 && length(model_aic) == 1 && length(model_bic) == 1 && !is.na(adj_r2) && !is.na(model_aic) && !is.na(model_bic)){
      model_performance <- rbind(model_performance,
                                 data.frame(Model = model_name,
                                            Adjusted_R2 = adj_r2,
                                            AIC = model_aic,
                                            BIC = model_bic,
                                            stringsAsFactors = FALSE))
    } else {
      cat("Skipping model performance metrics for:", model_name, "due to NA values.\n")
    }
  }
  
  # Identify Models that Pass All Assumptions
  acceptable_models <- c()
  
  for(model_name in names(diagnostic_results)) {
    diagnostics <- diagnostic_results[[model_name]]
    
    # Criteria for Passing Assumptions:
    # 1. Shapiro-Wilk p-value > 0.05 (Normality)
    # 2. Breusch-Pagan p-value > 0.05 (Homoskedasticity)
    # 3. RESET Test p-value > 0.05 (Correct Functional Form)
    # 4. If multiple predictors, VIF < 5 (Multicollinearity)
    # 5. If time_series = TRUE, Breusch-Godfrey p-value > 0.05 (No Autocorrelation)
    
    pass_shapiro <- if(!is.na(diagnostics$shapiro$p.value)){
      diagnostics$shapiro$p.value > 0.05
    } else { FALSE }
    
    pass_bptest <- if(!is.na(diagnostics$bptest$p.value)){
      diagnostics$bptest$p.value > 0.05
    } else { FALSE }
    
    pass_reset <- if(!is.na(diagnostics$reset$p.value)){
      diagnostics$reset$p.value > 0.05
    } else { FALSE }
    
    pass_vif <- TRUE
    if(!is.na(diagnostics$vif[1])) {
      pass_vif <- all(diagnostics$vif < 5)
    }
    
    pass_bgtest <- TRUE
    if(time_series) {
      if(!is.na(diagnostics$bgtest$p.value)){
        pass_bgtest <- diagnostics$bgtest$p.value > 0.05
      } else {
        pass_bgtest <- FALSE
      }
    }
    
    # Determine if Model Passes All Tests
    if(pass_shapiro && pass_bptest && pass_reset && pass_vif && pass_bgtest) {
      acceptable_models <- c(acceptable_models, model_name)
      cat("\nModel", model_name, "passes all assumptions.\n")
    } else {
      cat("\nModel", model_name, "does NOT pass all assumptions.\n")
    }
  }
  
  # Select the Best Model Among Acceptable Models
  best_model <- NULL
  best_model_name <- NULL
  
  if(length(acceptable_models) > 0) {
    # Select the model with the highest Adjusted R-squared among acceptable models
    subset_performance <- model_performance[model_performance$Model %in% acceptable_models, ]
    best_model_name <- subset_performance$Model[which.max(subset_performance$Adjusted_R2)]
    
    if(best_model_name %in% names(predefined_alternatives)){
      best_model_formula <- predefined_alternatives[[best_model_name]]
      best_model <- lm(best_model_formula, data = data)
      cat("\n=== Best Model Selected:", best_model_name, "===\n")
      print(summary(best_model))
    } else {
      cat("\nBest model name not found in predefined_alternatives. Exiting.\n")
      return(NULL)
    }
    
  } else {
    cat("\nNo models passed all assumption checks.\n")
    cat("Selecting the model with the highest Adjusted R-squared among all models.\n")
    
    # Check if 'model_performance' has any models
    if(nrow(model_performance) > 0){
      best_model_name <- model_performance$Model[which.max(model_performance$Adjusted_R2)]
      if(best_model_name %in% names(predefined_alternatives)){
        best_model_formula <- predefined_alternatives[[best_model_name]]
        best_model <- lm(best_model_formula, data = data)
        cat("Model selected:", best_model_name, "\n")
        cat("However, this model does not satisfy all diagnostic assumptions.\n")
      } else {
        cat("Best model name not found in predefined_alternatives. Exiting.\n")
        return(NULL)
      }
    } else {
      cat("No models were successfully fitted. Exiting function.\n")
      return(NULL)
    }
  }
  
  # Automated Interpretation of the Best Model
  interpret_model <- function(lm_model, formula_used) {
    cat("\n=== Model Interpretation ===\n")
    coefficients <- coef(lm_model)
    # Check if log transformations are used
    transformed_Y <- grepl("log\\(", as.character(formula_used)[3])
    
    if(transformed_Y){
      if(all(grepl("log\\(", names(coefficients)))){
        # Log-Log Model: Coefficients are elasticities
        cat("This is a Log-Log model. Coefficients represent elasticities.\n")
        for(i in 2:length(coefficients)){
          cat(sprintf("A 1%% increase in %s is associated with a %.2f%% change in %s.\n",
                      names(coefficients)[i], coefficients[i]*100, response))
        }
      } else {
        # Other types of transformed models
        cat("Response variable is log-transformed. Coefficients represent semi-elasticities.\n")
        for(i in 2:length(coefficients)){
          cat(sprintf("A 1 unit increase in %s is associated with a %.2f%% change in %s.\n",
                      names(coefficients)[i], coefficients[i]*100, response))
        }
      }
    } else {
      # Linear Model: Coefficients represent unit changes
      cat("This is a Linear model. Coefficients represent unit changes.\n")
      for(i in 2:length(coefficients)){
        cat(sprintf("A one unit increase in %s is associated with a %.2f unit change in %s.\n",
                    names(coefficients)[i], coefficients[i], response))
      }
    }
  }
  
  if(!is.null(best_model)){
    interpret_model(best_model, best_model_formula)
  }
  
  # Plot diagnostics for the Best Model with Enhanced Features
  if(!is.null(best_model)){
    # Set up plot area for 4 plots per page
    par(mfrow=c(2,2))
    plot(best_model, main = paste("Final Model Diagnostic Plots -", best_model_name))
    par(mfrow=c(1,1))
    
    # Residuals vs Time (if applicable)
    if(time_series && "Time" %in% names(data)){
      par(mfrow=c(2,2))
      plot(resid(best_model) ~ data$Time, type = 'l', main = "Residuals over Time",
           xlab = "Time", ylab = "Residuals")
      abline(h=0, lty = 2, col = "red")
      
      # Fitted vs Actual
      fitted_vals <- fitted(best_model)
      plot(fitted_vals, data[[response]], main = "Fitted vs Actual",
           xlab = "Fitted Values", ylab = "Actual Values")
      abline(0,1, col = "blue", lty = 2)
      
      # Partial Residual Plots
      if(length(coef(best_model)) > 2){
        crPlots(best_model, main = "Component + Residual Plots")
      }
      
      # Additional Diagnostic or Custom Plot (optional)
      # For example, Scale-Location plot
      plot(best_model, which = 3, main = "Scale-Location Plot")
      par(mfrow=c(1,1))
    }
  }
  
  # Perform Diagnostics on Final Model
  final_diagnostics <- list()
  if(!is.null(best_model)){
    final_diagnostics <- perform_diagnostics(best_model, time_series)
  }
  
  # Print Final Diagnostic Test Results
  cat("\n--- Final Diagnostic Tests for Best Model ---\n")
  if(!is.null(final_diagnostics$shapiro) && !is.na(final_diagnostics$shapiro$p.value)){
    cat("Shapiro-Wilk Test for Normality:\n")
    print(final_diagnostics$shapiro)
  }
  
  if(!is.null(final_diagnostics$bptest) && !is.na(final_diagnostics$bptest$p.value)){
    cat("\nBreusch-Pagan Test for Heteroskedasticity:\n")
    print(final_diagnostics$bptest)
  }
  
  if(!is.null(final_diagnostics$reset) && !is.na(final_diagnostics$reset$p.value)){
    cat("\nRESET Test for Functional Form:\n")
    print(final_diagnostics$reset)
  }
  
  if(!is.null(final_diagnostics$vif)){
    cat("\nVariance Inflation Factor (VIF):\n")
    print(final_diagnostics$vif)
  } else {
    cat("\nVIF not applicable (only one predictor).\n")
  }
  
  if(time_series && !is.null(final_diagnostics$bgtest) && !is.na(final_diagnostics$bgtest$p.value)){
    cat("\nBreusch-Godfrey Test for Autocorrelation:\n")
    print(final_diagnostics$bgtest)
  }
  
  # Check if All Assumptions Pass
  pass_shapiro_final <- if(!is.null(final_diagnostics$shapiro) && !is.na(final_diagnostics$shapiro$p.value)) final_diagnostics$shapiro$p.value > 0.05 else FALSE

  pass_bptest_final <- if(!is.null(final_diagnostics$bptest) && !is.na(final_diagnostics$bptest$p.value)) final_diagnostics$bptest$p.value > 0.05 else FALSE

  pass_reset_final <- if(!is.null(final_diagnostics$reset) && !is.na(final_diagnostics$reset$p.value)) final_diagnostics$reset$p.value > 0.05 else FALSE

  pass_vif_final <- TRUE
  if(!is.na(final_diagnostics$vif[1])) {
    pass_vif_final <- all(final_diagnostics$vif < 5)
  }

  pass_bgtest_final <- TRUE
  if(time_series) {
    pass_bgtest_final <- if(!is.null(final_diagnostics$bgtest) && !is.na(final_diagnostics$bgtest$p.value)) final_diagnostics$bgtest$p.value > 0.05 else FALSE
  }

  if(pass_shapiro_final && pass_bptest_final && pass_reset_final && pass_vif_final && pass_bgtest_final) {
    cat("\nFinal model satisfies all assumptions.\n")
  } else {
    cat("\nFinal model has some assumption violations.\n")
    cat("Consider further model refinements or consult additional diagnostics.\n")
  }
  
  # Calculate and Display Robust Standard Errors
  cat("\n=== Final Model with Robust Standard Errors ===\n")
  if(!is.null(best_model)){
    robust_se <- tryCatch({
      coeftest(best_model, vcov = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute robust standard errors.\n")
      NA
    })
    print(robust_se)
  }
  
  # Calculate and Display 95% Confidence Intervals using Robust SE
  cat("\n=== 95% Confidence Intervals (Robust SE) ===\n")
  if(!is.null(best_model)){
    ci <- tryCatch({
      confint(best_model, vcov. = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute confidence intervals.\n")
      NA
    })
    print(ci)
  }
  
  # Calculate Effect Sizes (Eta-squared)
  cat("\n=== Effect Size (Eta-squared) ===\n")
  if(!is.null(best_model)){
    anova_result <- tryCatch({
      Anova(best_model, type = "II")
    }, error = function(e) {
      anova(best_model)
    })
    if("Sum Sq" %in% names(anova_result)){
      ss_total <- sum(anova_result[,"Sum Sq"], na.rm = TRUE)
      ss_effect <- anova_result[,"Sum Sq"]
      eta_sq <- ss_effect / ss_total
      names(eta_sq) <- rownames(anova_result)
      print(round(eta_sq, 4))
    } else {
      cat("ANOVA results do not contain 'Sum Sq'. Unable to compute effect sizes.\n")
    }
  }
  
  # Automated Multiple Comparisons Handling (if Interaction Exists and is Significant)
  if(!is.null(best_model)){
    # Check if interaction exists in the model
    interaction_terms <- grep(":", names(coef(best_model)), value = TRUE)
    if(length(interaction_terms) > 0){
      # Check if interactions are significant
      anova_table <- tryCatch({
        Anova(best_model, type = "II")
      }, error = function(e) {
        anova(best_model)
      })
      
      # Extract p-values for interaction terms
      interaction_p_values <- sapply(interaction_terms, function(term){
        if(term %in% rownames(anova_table)){
          return(anova_table[term, "Pr(>F)"])
        } else {
          return(NA)
        }
      })
      
      # Determine if any interaction term is significant
      significant_interactions <- interaction_terms[which(!is.na(interaction_p_values) & interaction_p_values < 0.05)]
      
      if(length(significant_interactions) > 0){
        cat("\nInteraction effects are significant. Performing multiple comparisons for interaction groups.\n")
        
        # Create Interaction Groups based on significant interactions
        # Assuming only pairwise interactions
        interaction_factors <- unique(unlist(strsplit(significant_interactions, ":")))
        interaction_group_var <- paste(interaction_factors, collapse = ":")
        data$Interaction_Group <- interaction(data[,interaction_factors], sep = ":")
        
        # Fit a Model with Interaction Groups
        interaction_model <- lm(formula = as.formula(paste("transformed_response ~ Interaction_Group")), data = data)
        
        # Perform Tukey's HSD using glht
        tukey_results <- tryCatch({
          glht(interaction_model, linfct = mcp(Interaction_Group = "Tukey"))
        }, error = function(e) {
          cat("Failed to perform Tukey's HSD for interaction groups.\n")
          NULL
        })
        
        if(!is.null(tukey_results)){
          cat("\n=== Tukey's HSD Results for Interaction Groups ===\n")
          print(summary(tukey_results))
          # Plot Tukey's HSD
          plot(tukey_results, main = "Tukey HSD for Interaction Groups")
        }
      } else {
        cat("\nNo significant interaction effects found. Performing multiple comparisons for main effects.\n")
        
        # Identify main effects
        main_effects <- setdiff(factors, unique(unlist(strsplit(interaction_terms, ":"))))
        
        for(factor in main_effects){
          cat("\nPerforming Tukey's HSD for Factor:", factor, "\n")
          # Perform Tukey's HSD using glht
          tukey_results <- tryCatch({
            glht(best_model, linfct = mcp(setNames(list("Tukey"), factor) ))
          }, error = function(e) {
            cat("Failed to perform Tukey's HSD for factor:", factor, "\n")
            NULL
          })
          
          if(!is.null(tukey_results)){
            print(summary(tukey_results))
            # Plot Tukey's HSD
            plot(tukey_results, main = paste("Tukey HSD for", factor))
          }
        }
      }
    }
  }
  
  # Return a List Containing All Relevant Outputs
  return(list(
    best_model = best_model,
    summary = summary(best_model),
    diagnostics = diagnostic_results[[best_model_name]],
    robust_summary = if(!is.null(best_model)) tryCatch({
      coeftest(best_model, vcov = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute robust standard errors.\n")
      NA
    }) else NA,
    confidence_intervals = if(!is.null(best_model)) tryCatch({
      confint(best_model, vcov. = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute confidence intervals.\n")
      NA
    }) else NA,
    anova_table = if(!is.null(best_model)) tryCatch({
      Anova(best_model, type = "II")
    }, error = function(e) {
      anova(best_model)
    }) else NA,
    effect_sizes = if(!is.null(best_model)) {
      if("Sum Sq" %in% names(Anova(best_model, type = "II"))){
        ss_total <- sum(Anova(best_model, type = "II")$`Sum Sq`, na.rm = TRUE)
        ss_effect <- Anova(best_model, type = "II")$`Sum Sq`
        eta_sq <- ss_effect / ss_total
        names(eta_sq) <- rownames(Anova(best_model, type = "II"))
        round(eta_sq, 4)
      } else {
        NA
      }
    } else {
      NA
    },
    multiple_comparisons = NULL,  # Can be extended to store Tukey results
    models_tried = predefined_alternatives,
    model_performance = model_performance
  ))
}
```

### **Key Corrections and Enhancements:**

1. **Robust Diagnostic Handling:**
   - **Consistent List Structure:** Modified the `perform_diagnostics` function to ensure that each diagnostic test (Shapiro-Wilk, Breusch-Pagan, RESET, Breusch-Godfrey) always returns a list containing a `p.value`. If a test fails due to an error, it gracefully returns `p.value = NA` instead of an atomic vector.
   - **Error Prevention:** Utilized `tryCatch` within the `perform_diagnostics` function to handle potential errors during diagnostic tests, preventing the function from terminating unexpectedly.

2. **Safe Model Correction:**
   - **Conditional Correction Steps:** The `correct_model` function now checks whether corrections (like `varPower` for heteroskedasticity or `corAR1` for autocorrelation) are applicable before attempting to apply them.
   - **Graceful Failure Handling:** If applying a correction fails (e.g., due to incompatible data structure), the function notifies the user and skips the correction step without causing further errors.

3. **Accurate Logical Conditions:**
   - **Single Logical Values:** Ensured that all logical conditions (e.g., checking if `bptest$p.value > 0.05`) are based on single `TRUE` or `FALSE` values, thereby preventing errors related to vector coercion.

4. **Enhanced Plotting:**
   - **Multiple Plots Layout:** Configured the plotting function to arrange multiple diagnostic plots (four per page) for better visualization. The `par(mfrow=c(2,2))` setting ensures that up to four plots are displayed on the same page.
   - **Residuals Over Time:** Included an additional plot for residuals over time if a `Time` variable exists and `time_series` is set to `TRUE`.

5. **Interaction Term Handling:**
   - **Automatic Inclusion:** The function now automatically includes interaction terms for predictors that are highly correlated (correlation coefficient > 0.7), aiding in capturing significant combined effects without unnecessarily complicating the model.

6. **Robust Model Selection:**
   - **Handling No Acceptable Models:** If no models pass all diagnostic checks, the function selects the model with the highest Adjusted R-squared among all fitted models, ensuring that a model is selected without causing errors.
   - **Consistent Row Lengths:** Added checks to ensure that only successfully fitted models with valid performance metrics are included in the `model_performance` data frame.

7. **Multiple Comparisons:**
   - **Automated Tukey's HSD:** When interaction effects are significant, the function automatically performs Tukey's HSD for interaction groups. If no interactions are significant, it performs Tukey's HSD for main effects.

8. **Comprehensive Reporting:**
   - **Effect Sizes (Eta-squared):** Calculated and reported Eta-squared values for effect sizes when possible.
   - **Clear Notifications:** The function provides clear console messages regarding the status of model assumptions and corrections applied, aiding in understanding the analysis flow.

## **3. Using the Corrected Function**

### **a. Ensure All Necessary Packages Are Installed and Loaded:**

```r
# Install and Load Necessary Packages
required_packages <- c("AER", "lmtest", "car", "sandwich", "ggplot2", "nlme", "multcomp", "plyr")
new_packages <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]
if(length(new_packages)) install.packages(new_packages)

# Load Libraries
library(AER)
library(lmtest)
library(car)
library(sandwich)
library(ggplot2)
library(nlme)
library(multcomp)
library(plyr)
```

### **b. Define the Corrected `automated_regression_analysis` Function:**

*(Use the corrected function code provided above.)*

### **c. Load Your Dataset and Run the Analysis:**

```r
# Load your regression dataset
Wheat <- read.csv('NorthamptonWheat.csv')

# Perform Automated Regression Analysis
regression_results <- automated_regression_analysis(
  data = Wheat,
  formula = Northampton ~ Time,
  time_series = TRUE  # Set to TRUE if your data is time series
)

# Accessing the Best Model Summary
summary(regression_results$best_model)

# Accessing Diagnostics
regression_results$diagnostics

# Accessing Robust Standard Errors
regression_results$robust_summary

# Accessing Confidence Intervals
regression_results$confidence_intervals

# Accessing ANOVA Table
regression_results$anova_table

# Accessing Effect Sizes
regression_results$effect_sizes

# Accessing Multiple Comparisons Results (if applicable)
regression_results$multiple_comparisons
```

## **4. Addressing Multicollinearity**

In your previous output, you encountered high Variance Inflation Factor (VIF) values (>5) for predictors:

```
Variance Inflation Factor (VIF):
     Time I(Time^3) 
6.411861 6.411861 
```

**Implications:**
- **Multicollinearity Issue:** High VIF values indicate that the predictors are highly correlated, which can inflate the variance of coefficient estimates and make them unstable.

**Recommendations:**

1. **Remove One of the Highly Correlated Predictors:**
   - **Decision Basis:** Determine which predictor to remove based on theoretical considerations or the one with a higher p-value.
   
   ```r
   # Suppose you decide to remove I(Time^3) due to high VIF
   regression_results <- automated_regression_analysis(
     data = Wheat,
     formula = Northampton ~ Time,  # Ensure I(Time^3) is not included in the formula
     time_series = TRUE
   )
   
   # Verify if VIF is now acceptable
   print(regression_results$diagnostics$vif)
   ```

2. **Combine Predictors:**
   - **Composite Variable:** Create a new variable that captures the combined effect of the highly correlated predictors.
   
   ```r
   # Example of creating a composite variable (if applicable)
   Wheat$Combined_Time <- Wheat$Time + Wheat$I_Time3  # Replace with actual variable names
   ```

3. **Regularization Techniques:**
   - **Ridge Regression or Lasso:** These techniques can help manage multicollinearity by imposing penalties on the size of coefficients.
   
   ```r
   # Example using Ridge Regression (requires glmnet package)
   install.packages("glmnet")
   library(glmnet)
   
   # Prepare data
   x <- model.matrix(Northampton ~ Time + I(Time^2) + I(Time^3), Wheat)[,-1]
   y <- Wheat$Northampton
   
   # Fit Ridge Regression
   ridge_model <- glmnet(x, y, alpha = 0)
   plot(ridge_model)
   ```

4. **Centering or Scaling:**
   - **Standardization:** Centering and scaling predictors can sometimes reduce multicollinearity.
   
   ```r
   # Centering and scaling 'Time'
   Wheat$Time_scaled <- scale(Wheat$Time)
   ```

## **5. Enhanced Diagnostic Plotting**

To display multiple diagnostic plots on the same page with four plots per page, the function has been adjusted to set up the plotting area accordingly. Here's how it's handled within the function:

```r
# Function to plot diagnostics
plot_diagnostics <- function(lm_model, model_type, time_series_flag) {
  # Set up plot area for 4 plots per page
  par(mfrow=c(2,2))
  
  # Standard Diagnostic Plots
  plot(lm_model, main = paste("Diagnostic Plots -", model_type))
  
  # Residuals over Time (if applicable)
  if(time_series_flag && "Time" %in% names(data)){
    plot(resid(lm_model) ~ data$Time, type = 'l', main = paste("Residuals over Time -", model_type),
         xlab = "Time", ylab = "Residuals")
    abline(h=0, lty = 2, col = "red")
  }
  
  # Fitted vs Actual Values
  plot(fitted(lm_model), data[[response]], main = paste("Fitted vs Actual -", model_type),
       xlab = "Fitted Values", ylab = "Actual Values")
  abline(0,1, col = "blue", lty = 2)
  lines(fitted(lm_model), fitted(lm_model), col = "green")
  
  # Partial Residual Plots
  if(length(coef(lm_model)) > 2){
    crPlots(lm_model, main = paste("Component + Residual Plots -", model_type))
  }
  
  # Reset plot layout to default
  par(mfrow=c(1,1))
}
```

**Explanation:**
- **`par(mfrow=c(2,2))`:** Splits the plotting window into a 2x2 matrix, allowing four plots to be displayed on the same page.
- **Diagnostic Plots Included:**
  1. **Standard Diagnostic Plots:** Residuals vs Fitted, QQ-Plot, Scale-Location, and Residuals vs Leverage.
  2. **Residuals over Time:** Useful for time series data to check for patterns indicating autocorrelation.
  3. **Fitted vs Actual Values:** Assesses the fit of the model.
  4. **Component + Residual Plots:** Helps in diagnosing the relationship between predictors and response.
- **Resetting Plot Layout:** After plotting, `par(mfrow=c(1,1))` resets the plotting layout to default, ensuring subsequent plots are not affected.

## **6. Final Function Implementation**

Here's the **complete corrected function** with all the enhancements and error handling incorporated:

```r
# Define the Enhanced Automated Regression Analysis Function
automated_regression_analysis <- function(data, formula, time_series = FALSE) {
  
  # Extract Response and Predictor Names
  response <- all.vars(formula)[1]
  predictors <- all.vars(formula)[-1]
  
  # Automatically Detect and Create Interaction Terms Based on Correlation
  # Interactions are created only for highly correlated predictors (correlation > 0.7)
  if(length(predictors) > 1){
    predictor_combinations <- combn(predictors, 2, simplify = FALSE)
    high_corr_interactions <- sapply(predictor_combinations, function(pair) {
      cor_val <- cor(data[[pair[1]]], data[[pair[2]]], use = "complete.obs")
      if(abs(cor_val) > 0.7) {
        return(paste(pair, collapse = ":"))
      } else {
        return(NULL)
      }
    })
    high_corr_interactions <- high_corr_interactions[!sapply(high_corr_interactions, is.null)]
  } else {
    high_corr_interactions <- character(0)
  }
  
  # Create a List of Predefined Alternative Models with Interaction Terms
  predefined_alternatives <- list(
    "Linear" = formula,
    "Log-Linear" = as.formula(paste("log(", response, ") ~ ", paste(predictors, collapse = " + "), sep = "")),
    "Log-Log" = as.formula(paste("log(", response, ") ~ ", paste(paste0("log(", predictors, ")"), collapse = " + "), sep = "")),
    "Quadratic" = as.formula(
      paste(
        response, "~", 
        paste(predictors, collapse = " + "), 
        "+", 
        paste(paste0("I(", predictors, "^2)"), collapse = " + ")
      )
    ),
    "Cubic" = as.formula(
      paste(
        response, "~", 
        paste(predictors, collapse = " + "), 
        "+", 
        paste(paste0("I(", predictors, "^3)"), collapse = " + ")
      )
    )
  )
  
  # Add Interaction Models if High Correlation Exists
  if(length(high_corr_interactions) > 0){
    interaction_formula <- as.formula(
      paste(response, "~", paste(c(predictors, high_corr_interactions), collapse = " + "))
    )
    predefined_alternatives[["Interactions"]] <- interaction_formula
  }
  
  # Initialize Storage for Models and Their Diagnostics
  model_summaries <- list()
  diagnostic_results <- list()
  model_performance <- data.frame(Model = character(),
                                  Adjusted_R2 = numeric(),
                                  AIC = numeric(),
                                  BIC = numeric(),
                                  stringsAsFactors = FALSE)
  
  # Define a Helper Function to Perform Diagnostics
  perform_diagnostics <- function(lm_model, time_series_flag) {
    diagnostics <- list()
    
    # Shapiro-Wilk Test for Normality of Residuals
    diagnostics$shapiro <- tryCatch({
      shapiro.test(lm_model$residuals)
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # Breusch-Pagan Test for Heteroskedasticity
    diagnostics$bptest <- tryCatch({
      bptest(lm_model)
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # RESET Test for Functional Form
    diagnostics$reset <- tryCatch({
      resettest(lm_model, power = 2:3, type = "fitted")
    }, error = function(e) {
      list(p.value = NA)
    })
    
    # Variance Inflation Factor (VIF) for Multicollinearity (if multiple predictors)
    if(length(coef(lm_model)) > 2) {  # More than one predictor
      diagnostics$vif <- tryCatch({
        vif(lm_model)
      }, error = function(e) {
        NA
      })
    } else {
      diagnostics$vif <- NA
    }
    
    # Breusch-Godfrey Test for Autocorrelation (if time_series = TRUE)
    if(time_series_flag) {
      diagnostics$bgtest <- tryCatch({
        bgtest(lm_model)
      }, error = function(e) {
        list(p.value = NA)
      })
    } else {
      diagnostics$bgtest <- NA
    }
    
    return(diagnostics)
  }
  
  # Define a Helper Function to Correct for Heteroskedasticity and Autocorrelation
  correct_model <- function(lm_model, diagnostics, time_series_flag, data, formula_used) {
    corrected_model <- NULL
    correction_steps <- c()
    
    # Correct for Heteroskedasticity using GLS with variance structures
    if(!is.na(diagnostics$bptest$p.value) && diagnostics$bptest$p.value < 0.05){
      cat("Heteroskedasticity detected. Applying GLS with varPower.\n")
      corrected_model <- tryCatch({
        gls(formula_used, data = data, weights = varPower())
      }, error = function(e) {
        cat("Failed to apply varPower for heteroskedasticity correction.\n")
        NULL
      })
      if(!is.null(corrected_model)){
        correction_steps <- c(correction_steps, "Heteroskedasticity corrected with varPower")
      }
    }
    
    # Correct for Autocorrelation using GLS with AR(1) structure
    if(time_series_flag && !is.na(diagnostics$bgtest$p.value) && diagnostics$bgtest$p.value < 0.05){
      cat("Autocorrelation detected. Applying GLS with AR(1) correlation structure.\n")
      if(is.null(corrected_model)){
        corrected_model <- tryCatch({
          gls(formula_used, data = data, correlation = corAR1())
        }, error = function(e) {
          cat("Failed to apply AR(1) for autocorrelation correction.\n")
          NULL
        })
      } else {
        corrected_model <- tryCatch({
          update(corrected_model, correlation = corAR1())
        }, error = function(e) {
          cat("Failed to update GLS model with AR(1) correlation structure.\n")
          NULL
        })
      }
      if(!is.null(corrected_model)){
        correction_steps <- c(correction_steps, "Autocorrelation corrected with AR(1)")
      }
    }
    
    # Return the Corrected Model and Steps Taken
    return(list(model = corrected_model, steps = correction_steps))
  }
  
  # Function to plot diagnostics
  plot_diagnostics <- function(lm_model, model_type, time_series_flag) {
    # Set up plot area for 4 plots per page
    par(mfrow=c(2,2))
    
    # Standard Diagnostic Plots
    plot(lm_model, main = paste("Diagnostic Plots -", model_type))
    
    # Residuals over Time (if applicable)
    if(time_series_flag && "Time" %in% names(data)){
      plot(resid(lm_model) ~ data$Time, type = 'l', main = paste("Residuals over Time -", model_type),
           xlab = "Time", ylab = "Residuals")
      abline(h=0, lty = 2, col = "red")
    }
    
    # Fitted vs Actual Values
    plot(fitted(lm_model), data[[response]], main = paste("Fitted vs Actual -", model_type),
         xlab = "Fitted Values", ylab = "Actual Values")
    abline(0,1, col = "blue", lty = 2)
    lines(fitted(lm_model), fitted(lm_model), col = "green")
    
    # Partial Residual Plots
    if(length(coef(lm_model)) > 2){
      crPlots(lm_model, main = paste("Component + Residual Plots -", model_type))
    }
    
    # Reset plot layout to default
    par(mfrow=c(1,1))
  }
  
  # Fit All Predefined Alternative Models and Collect Diagnostics
  for(model_name in names(predefined_alternatives)) {
    cat("\n=== Fitting Model:", model_name, "===\n")
    current_formula <- predefined_alternatives[[model_name]]
    
    # Fit the Model
    lm_model <- tryCatch(lm(current_formula, data = data), silent = TRUE)
    
    if(class(lm_model) == "try-error") {
      cat("Failed to fit model:", model_name, "\n")
      next  # Skip to next model if fitting fails
    }
    
    cat("\n")
    print(summary(lm_model))
    
    # Perform Diagnostics
    diagnostics <- perform_diagnostics(lm_model, time_series)
    diagnostic_results[[model_name]] <- diagnostics
    
    # Correct the Model if Necessary
    corrections <- correct_model(lm_model, diagnostics, time_series, data, current_formula)
    if(!is.null(corrections$model)){
      lm_model_corrected <- corrections$model
      cat("Corrections Applied:", paste(corrections$steps, collapse = "; "), "\n")
      print(summary(lm_model_corrected))
      # Update diagnostics after correction
      diagnostics_corrected <- perform_diagnostics(lm_model_corrected, time_series)
      diagnostic_results[[model_name]] <- diagnostics_corrected
      # Replace the model with the corrected one
      lm_model <- lm_model_corrected
    }
    
    # Plot diagnostics
    plot_diagnostics(lm_model, model_name, time_series)
    
    # Store Model Summary
    model_summaries[[model_name]] <- summary(lm_model)
    
    # Calculate Model Performance Metrics
    adj_r2 <- summary(lm_model)$adj.r.squared
    model_aic <- AIC(lm_model)
    model_bic <- BIC(lm_model)
    
    # Ensure all performance metrics have length 1
    # Append only if all metrics are non-NA and single
    if(length(adj_r2) == 1 && length(model_aic) == 1 && length(model_bic) == 1 && !is.na(adj_r2) && !is.na(model_aic) && !is.na(model_bic)){
      model_performance <- rbind(model_performance,
                                 data.frame(Model = model_name,
                                            Adjusted_R2 = adj_r2,
                                            AIC = model_aic,
                                            BIC = model_bic,
                                            stringsAsFactors = FALSE))
    } else {
      cat("Skipping model performance metrics for:", model_name, "due to NA values.\n")
    }
  }
  
  # Identify Models that Pass All Assumptions
  acceptable_models <- c()
  
  for(model_name in names(diagnostic_results)) {
    diagnostics <- diagnostic_results[[model_name]]
    
    # Criteria for Passing Assumptions:
    # 1. Shapiro-Wilk p-value > 0.05 (Normality)
    # 2. Breusch-Pagan p-value > 0.05 (Homoskedasticity)
    # 3. RESET Test p-value > 0.05 (Correct Functional Form)
    # 4. If multiple predictors, VIF < 5 (Multicollinearity)
    # 5. If time_series = TRUE, Breusch-Godfrey p-value > 0.05 (No Autocorrelation)
    
    pass_shapiro <- if(!is.na(diagnostics$shapiro$p.value)){
      diagnostics$shapiro$p.value > 0.05
    } else { FALSE }
    
    pass_bptest <- if(!is.na(diagnostics$bptest$p.value)){
      diagnostics$bptest$p.value > 0.05
    } else { FALSE }
    
    pass_reset <- if(!is.na(diagnostics$reset$p.value)){
      diagnostics$reset$p.value > 0.05
    } else { FALSE }
    
    pass_vif <- TRUE
    if(!is.na(diagnostics$vif[1])) {
      pass_vif <- all(diagnostics$vif < 5)
    }
    
    pass_bgtest <- TRUE
    if(time_series) {
      if(!is.na(diagnostics$bgtest$p.value)){
        pass_bgtest <- diagnostics$bgtest$p.value > 0.05
      } else {
        pass_bgtest <- FALSE
      }
    }
    
    # Determine if Model Passes All Tests
    if(pass_shapiro && pass_bptest && pass_reset && pass_vif && pass_bgtest) {
      acceptable_models <- c(acceptable_models, model_name)
      cat("\nModel", model_name, "passes all assumptions.\n")
    } else {
      cat("\nModel", model_name, "does NOT pass all assumptions.\n")
    }
  }
  
  # Select the Best Model Among Acceptable Models
  best_model <- NULL
  best_model_name <- NULL
  
  if(length(acceptable_models) > 0) {
    # Select the model with the highest Adjusted R-squared among acceptable models
    subset_performance <- model_performance[model_performance$Model %in% acceptable_models, ]
    best_model_name <- subset_performance$Model[which.max(subset_performance$Adjusted_R2)]
    
    if(best_model_name %in% names(predefined_alternatives)){
      best_model_formula <- predefined_alternatives[[best_model_name]]
      best_model <- lm(best_model_formula, data = data)
      cat("\n=== Best Model Selected:", best_model_name, "===\n")
      print(summary(best_model))
    } else {
      cat("\nBest model name not found in predefined_alternatives. Exiting.\n")
      return(NULL)
    }
    
  } else {
    cat("\nNo models passed all assumption checks.\n")
    cat("Selecting the model with the highest Adjusted R-squared among all models.\n")
    
    # Check if 'model_performance' has any models
    if(nrow(model_performance) > 0){
      best_model_name <- model_performance$Model[which.max(model_performance$Adjusted_R2)]
      if(best_model_name %in% names(predefined_alternatives)){
        best_model_formula <- predefined_alternatives[[best_model_name]]
        best_model <- lm(best_model_formula, data = data)
        cat("Model selected:", best_model_name, "\n")
        cat("However, this model does not satisfy all diagnostic assumptions.\n")
      } else {
        cat("Best model name not found in predefined_alternatives. Exiting.\n")
        return(NULL)
      }
    } else {
      cat("No models were successfully fitted. Exiting function.\n")
      return(NULL)
    }
  }
  
  # Automated Interpretation of the Best Model
  interpret_model <- function(lm_model, formula_used) {
    cat("\n=== Model Interpretation ===\n")
    coefficients <- coef(lm_model)
    # Check if log transformations are used
    transformed_Y <- grepl("log\\(", as.character(formula_used)[3])
    
    if(transformed_Y){
      if(all(grepl("log\\(", names(coefficients)))){
        # Log-Log Model: Coefficients are elasticities
        cat("This is a Log-Log model. Coefficients represent elasticities.\n")
        for(i in 2:length(coefficients)){
          cat(sprintf("A 1%% increase in %s is associated with a %.2f%% change in %s.\n",
                      names(coefficients)[i], coefficients[i]*100, response))
        }
      } else {
        # Other types of transformed models
        cat("Response variable is log-transformed. Coefficients represent semi-elasticities.\n")
        for(i in 2:length(coefficients)){
          cat(sprintf("A 1 unit increase in %s is associated with a %.2f%% change in %s.\n",
                      names(coefficients)[i], coefficients[i]*100, response))
        }
      }
    } else {
      # Linear Model: Coefficients represent unit changes
      cat("This is a Linear model. Coefficients represent unit changes.\n")
      for(i in 2:length(coefficients)){
        cat(sprintf("A one unit increase in %s is associated with a %.2f unit change in %s.\n",
                    names(coefficients)[i], coefficients[i], response))
      }
    }
  }
  
  if(!is.null(best_model)){
    interpret_model(best_model, best_model_formula)
  }
  
  # Plot diagnostics for the Best Model with Enhanced Features
  if(!is.null(best_model)){
    # Set up plot area for 4 plots per page
    par(mfrow=c(2,2))
    plot(best_model, main = paste("Final Model Diagnostic Plots -", best_model_name))
    par(mfrow=c(1,1))
    
    # Residuals vs Time (if applicable)
    if(time_series && "Time" %in% names(data)){
      par(mfrow=c(2,2))
      plot(resid(best_model) ~ data$Time, type = 'l', main = "Residuals over Time",
           xlab = "Time", ylab = "Residuals")
      abline(h=0, lty = 2, col = "red")
      
      # Fitted vs Actual
      fitted_vals <- fitted(best_model)
      plot(fitted_vals, data[[response]], main = "Fitted vs Actual",
           xlab = "Fitted Values", ylab = "Actual Values")
      abline(0,1, col = "blue", lty = 2)
      
      # Partial Residual Plots
      if(length(coef(best_model)) > 2){
        crPlots(best_model, main = "Component + Residual Plots")
      }
      
      # Additional Diagnostic or Custom Plot (optional)
      # For example, Scale-Location plot
      plot(best_model, which = 3, main = "Scale-Location Plot")
      par(mfrow=c(1,1))
    }
  }
  
  # Perform Diagnostics on Final Model
  final_diagnostics <- list()
  if(!is.null(best_model)){
    final_diagnostics <- perform_diagnostics(best_model, time_series)
  }
  
  # Print Final Diagnostic Test Results
  cat("\n--- Final Diagnostic Tests for Best Model ---\n")
  if(!is.null(final_diagnostics$shapiro) && !is.na(final_diagnostics$shapiro$p.value)){
    cat("Shapiro-Wilk Test for Normality:\n")
    print(final_diagnostics$shapiro)
  }
  
  if(!is.null(final_diagnostics$bptest) && !is.na(final_diagnostics$bptest$p.value)){
    cat("\nBreusch-Pagan Test for Heteroskedasticity:\n")
    print(final_diagnostics$bptest)
  }
  
  if(!is.null(final_diagnostics$reset) && !is.na(final_diagnostics$reset$p.value)){
    cat("\nRESET Test for Functional Form:\n")
    print(final_diagnostics$reset)
  }
  
  if(!is.null(final_diagnostics$vif)){
    cat("\nVariance Inflation Factor (VIF):\n")
    print(final_diagnostics$vif)
  } else {
    cat("\nVIF not applicable (only one predictor).\n")
  }
  
  if(time_series && !is.null(final_diagnostics$bgtest) && !is.na(final_diagnostics$bgtest$p.value)){
    cat("\nBreusch-Godfrey Test for Autocorrelation:\n")
    print(final_diagnostics$bgtest)
  }
  
  # Check if All Assumptions Pass
  pass_shapiro_final <- if(!is.null(final_diagnostics$shapiro) && !is.na(final_diagnostics$shapiro$p.value)) final_diagnostics$shapiro$p.value > 0.05 else FALSE

  pass_bptest_final <- if(!is.null(final_diagnostics$bptest) && !is.na(final_diagnostics$bptest$p.value)) final_diagnostics$bptest$p.value > 0.05 else FALSE

  pass_reset_final <- if(!is.null(final_diagnostics$reset) && !is.na(final_diagnostics$reset$p.value)) final_diagnostics$reset$p.value > 0.05 else FALSE

  pass_vif_final <- TRUE
  if(!is.null(final_diagnostics$vif) && !all(is.na(final_diagnostics$vif))){
    pass_vif_final <- all(final_diagnostics$vif < 5)
  }

  pass_bgtest_final <- TRUE
  if(time_series) {
    pass_bgtest_final <- if(!is.null(final_diagnostics$bgtest) && !is.na(final_diagnostics$bgtest$p.value)) final_diagnostics$bgtest$p.value > 0.05 else FALSE
  }

  if(pass_shapiro_final && pass_bptest_final && pass_reset_final && pass_vif_final && pass_bgtest_final) {
    cat("\nFinal model satisfies all assumptions.\n")
  } else {
    cat("\nFinal model has some assumption violations.\n")
    cat("Consider further model refinements or consult additional diagnostics.\n")
  }
  
  # Calculate and Display Robust Standard Errors
  cat("\n=== Final Model with Robust Standard Errors ===\n")
  if(!is.null(best_model)){
    robust_se <- tryCatch({
      coeftest(best_model, vcov = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute robust standard errors.\n")
      NA
    })
    print(robust_se)
  }
  
  # Calculate and Display 95% Confidence Intervals using Robust SE
  cat("\n=== 95% Confidence Intervals (Robust SE) ===\n")
  if(!is.null(best_model)){
    ci <- tryCatch({
      confint(best_model, vcov. = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute confidence intervals.\n")
      NA
    })
    print(ci)
  }
  
  # Calculate Effect Sizes (Eta-squared)
  cat("\n=== Effect Size (Eta-squared) ===\n")
  if(!is.null(best_model)){
    anova_result <- tryCatch({
      Anova(best_model, type = "II")
    }, error = function(e) {
      anova(best_model)
    })
    if("Sum Sq" %in% names(anova_result)){
      ss_total <- sum(anova_result[,"Sum Sq"], na.rm = TRUE)
      ss_effect <- anova_result[,"Sum Sq"]
      eta_sq <- ss_effect / ss_total
      names(eta_sq) <- rownames(anova_result)
      print(round(eta_sq, 4))
    } else {
      cat("ANOVA results do not contain 'Sum Sq'. Unable to compute effect sizes.\n")
    }
  }
  
  # Automated Multiple Comparisons Handling (if Interaction Exists and is Significant)
  if(!is.null(best_model)){
    # Check if interaction exists in the model
    interaction_terms <- grep(":", names(coef(best_model)), value = TRUE)
    if(length(interaction_terms) > 0){
      # Check if interactions are significant
      anova_table <- tryCatch({
        Anova(best_model, type = "II")
      }, error = function(e) {
        anova(best_model)
      })
      
      # Extract p-values for interaction terms
      interaction_p_values <- sapply(interaction_terms, function(term){
        if(term %in% rownames(anova_table)){
          return(anova_table[term, "Pr(>F)"])
        } else {
          return(NA)
        }
      })
      
      # Determine if any interaction term is significant
      significant_interactions <- interaction_terms[which(!is.na(interaction_p_values) & interaction_p_values < 0.05)]
      
      if(length(significant_interactions) > 0){
        cat("\nInteraction effects are significant. Performing multiple comparisons for interaction groups.\n")
        
        # Create Interaction Groups based on significant interactions
        # Assuming only pairwise interactions
        interaction_factors <- unique(unlist(strsplit(significant_interactions, ":")))
        interaction_group_var <- paste(interaction_factors, collapse = ":")
        data$Interaction_Group <- interaction(data[,interaction_factors], sep = ":")
        
        # Fit a Model with Interaction Groups
        interaction_model <- lm(formula = as.formula(paste("transformed_response ~ Interaction_Group")), data = data)
        
        # Perform Tukey's HSD using glht
        tukey_results <- tryCatch({
          glht(interaction_model, linfct = mcp(Interaction_Group = "Tukey"))
        }, error = function(e) {
          cat("Failed to perform Tukey's HSD for interaction groups.\n")
          NULL
        })
        
        if(!is.null(tukey_results)){
          cat("\n=== Tukey's HSD Results for Interaction Groups ===\n")
          print(summary(tukey_results))
          # Plot Tukey's HSD
          plot(tukey_results, main = "Tukey HSD for Interaction Groups")
        }
      } else {
        cat("\nNo significant interaction effects found. Performing multiple comparisons for main effects.\n")
        
        # Identify main effects
        # Exclude factors involved in interactions
        main_effects <- setdiff(predictors, unique(unlist(strsplit(interaction_terms, ":"))))
        
        for(factor in main_effects){
          cat("\nPerforming Tukey's HSD for Factor:", factor, "\n")
          # Perform Tukey's HSD using glht
          tukey_results <- tryCatch({
            glht(best_model, linfct = mcp(setNames(list("Tukey"), factor) ))
          }, error = function(e) {
            cat("Failed to perform Tukey's HSD for factor:", factor, "\n")
            NULL
          })
          
          if(!is.null(tukey_results)){
            print(summary(tukey_results))
            # Plot Tukey's HSD
            plot(tukey_results, main = paste("Tukey HSD for", factor))
          }
        }
      }
    }
  }
  
  # Return a List Containing All Relevant Outputs
  return(list(
    best_model = best_model,
    summary = summary(best_model),
    diagnostics = diagnostic_results[[best_model_name]],
    robust_summary = if(!is.null(best_model)) tryCatch({
      coeftest(best_model, vcov = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute robust standard errors.\n")
      NA
    }) else NA,
    confidence_intervals = if(!is.null(best_model)) tryCatch({
      confint(best_model, vcov. = vcovHC(best_model, type = "HC3"))
    }, error = function(e) {
      cat("Failed to compute confidence intervals.\n")
      NA
    }) else NA,
    anova_table = if(!is.null(best_model)) tryCatch({
      Anova(best_model, type = "II")
    }, error = function(e) {
      anova(best_model)
    }) else NA,
    effect_sizes = if(!is.null(best_model)) {
      if("Sum Sq" %in% names(Anova(best_model, type = "II"))){
        ss_total <- sum(Anova(best_model, type = "II")$`Sum Sq`, na.rm = TRUE)
        ss_effect <- Anova(best_model, type = "II")$`Sum Sq`
        eta_sq <- ss_effect / ss_total
        names(eta_sq) <- rownames(Anova(best_model, type = "II"))
        round(eta_sq, 4)
      } else {
        NA
      }
    } else {
      NA
    },
    multiple_comparisons = NULL,  # Can be extended to store Tukey results
    models_tried = predefined_alternatives,
    model_performance = model_performance
  ))
}
```

### **Explanation of Corrections:**

1. **Diagnostic Tests Always Return a List with `p.value`:**
   - Modified the `perform_diagnostics` function to ensure that each diagnostic test (`shapiro`, `bptest`, `reset`, `bgtest`) returns a list containing the `p.value`, even if the test fails. This prevents the `$` operator from attempting to access `p.value` from an atomic vector.

2. **Safe Model Correction and Selection:**
   - Added checks to ensure that corrections (like applying `varPower` for heteroskedasticity or `corAR1` for autocorrelation) are only attempted if diagnostic tests indicate their necessity.
   - Implemented a fallback mechanism where, if no models pass all diagnostic checks, the function selects the model with the highest Adjusted R-squared among all fitted models without causing row mismatches.

3. **Enhanced Plotting with Multiple Plots Per Page:**
   - Configured the `plot_diagnostics` function to set up a 2x2 plotting area (`par(mfrow=c(2,2))`), allowing four diagnostic plots to be displayed on the same page for comprehensive visualization.
   - After plotting, the plot layout is reset to default (`par(mfrow=c(1,1))`) to prevent affecting subsequent plots.

4. **Consistent Data Frame Structure:**
   - Ensured that the `model_performance` data frame only includes models with valid performance metrics, preventing the previously encountered row mismatch error.

5. **Robust Handling of Multiple Comparisons:**
   - Expanded the multiple comparisons section to automatically perform Tukey's HSD for interaction groups if interaction effects are significant. If not, it performs Tukey's HSD for main effects.
   
6. **Comprehensive Error Handling:**
   - Utilized `tryCatch` extensively to handle potential errors gracefully, ensuring that the function continues executing even if certain steps fail.

## **7. Running the Corrected Function**

Follow these steps to execute the corrected `automated_regression_analysis` function without encountering errors:

### **a. Ensure All Necessary Packages Are Installed and Loaded:**

```r
# Install and Load Necessary Packages
required_packages <- c("AER", "lmtest", "car", "sandwich", "ggplot2", "nlme", "multcomp", "plyr")
new_packages <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]
if(length(new_packages)) install.packages(new_packages)

# Load Libraries
library(AER)
library(lmtest)
library(car)
library(sandwich)
library(ggplot2)
library(nlme)
library(multcomp)
library(plyr)
```

### **b. Define the Corrected Function:**

*(Use the corrected function code provided above.)*

### **c. Load Your Dataset and Run the Analysis:**

```r
# Load your regression dataset
Wheat <- read.csv('NorthamptonWheat.csv')

# Perform Automated Regression Analysis
regression_results <- automated_regression_analysis(
  data = Wheat,
  formula = Northampton ~ Time,
  time_series = TRUE  # Set to TRUE if your data is time series
)

# Accessing the Best Model Summary
summary(regression_results$best_model)

# Accessing Diagnostics
regression_results$diagnostics

# Accessing Robust Standard Errors
regression_results$robust_summary

# Accessing Confidence Intervals
regression_results$confidence_intervals

# Accessing ANOVA Table
regression_results$anova_table

# Accessing Effect Sizes
regression_results$effect_sizes

# Accessing Multiple Comparisons Results (if applicable)
regression_results$multiple_comparisons
```

### **d. Verify Multicollinearity:**

If the function detects high VIF values, consider the following steps:

1. **Remove High VIF Predictors:**
   - Decide based on theoretical justification which correlated predictor to retain.
   
   ```r
   # Example: If 'I(Time^3)' has high VIF, modify the formula to exclude it
   regression_results <- automated_regression_analysis(
     data = Wheat,
     formula = Northampton ~ Time,
     time_series = TRUE
   )
   
   # Check VIF to ensure it's now acceptable
   print(regression_results$diagnostics$vif)
   ```

2. **Combine Predictors or Use Regularization:**
   - Create a composite variable or apply techniques like Ridge Regression if removing predictors isn't feasible.

   ```r
   # Example of creating a composite variable
   # Wheat$Time_Composite <- Wheat$Time + (Wheat$Time)^2  # Adjust as necessary
   ```

   ```r
   # Example using Ridge Regression
   install.packages("glmnet")
   library(glmnet)
   
   # Prepare data for Ridge Regression
   x <- model.matrix(Northampton ~ Time + I(Time^2) + I(Time^3), Wheat)[,-1]
   y <- Wheat$Northampton
   
   # Fit Ridge Regression
   ridge_model <- glmnet(x, y, alpha = 0)
   plot(ridge_model)
   ```

## **8. Final Recommendations**

1. **Review Diagnostic Plots Carefully:**
   - Ensure that residuals are randomly scattered without discernible patterns.
   - Check QQ-plots to confirm the normality of residuals.
   - Examine the Scale-Location plot to verify homoskedasticity.

2. **Interpret Results in Context:**
   - Understand the practical implications of coefficient estimates, especially in the context of transformed models (e.g., log-log models imply elasticity).

3. **Iterative Model Refinement:**
   - Based on diagnostics, iteratively refine your model by adding/removing predictors, transforming variables, or adjusting interaction terms.

4. **Document Model Decisions:**
   - Keep a record of why certain predictors were removed or why specific transformations were applied to maintain transparency and reproducibility.

5. **Consult Additional Resources if Needed:**
   - For complex issues like multicollinearity or autocorrelation, consider consulting statistical references or seeking expert advice.

By implementing the corrected function and following these recommendations, your automated regression analysis should function seamlessly without the previously encountered errors, providing comprehensive insights and robust model evaluations.