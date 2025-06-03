
## Using RStudio Server on Kaya

1. First, load the R module and RStudio module:
    ```bash
    module load r/4.3.1  # Or another version that's compatible with your Seurat object
    module load rstudio/2023.12.1
    ```
    
2. Start an interactive session on the peb partition where you can run RStudio Server:
    
```
# For the "work" partition (3-day time limit)
srun --time=8:00:00 --account=sms029 --partition=work --nodes=1 --ntasks=1 --cpus-per-task=8 --mem-per-cpu=8G --pty /bin/bash -l

# Or for longer jobs, use the "long" partition (7-day time limit)
srun --time=8:00:00 --account=sms029 --partition=long --nodes=1 --ntasks=1 --cpus-per-task=8 --mem-per-cpu=8G --pty /bin/bash -l
```
    (Adjust the resources based on your needs - your .rds file is nearly 30GB so you'll need significant memory)
    
3. Once in the interactive session, start RStudio Server:
    
    ```bash
    module load r/4.3.1
    module load rstudio/2023.12.1
    rstudio-server start
    ```
    
4. Set up SSH port forwarding on your local machine to connect to the RStudio Server:
    
    ```bash
    ssh -L 8787:localhost:8787 mnieuwenh@kaya.hpc.uwa.edu.au
    ```
    
5. Access RStudio in your web browser by navigating to:
    
    ```
    http://localhost:8787
    ```
    
    Log in with your Kaya username and password.
    

## Working with the large Seurat object

For a 30GB .rds file:

1. Make sure you request enough memory in your interactive session (at least 60GB total would be good)
2. When loading the object in R:
    
    ```r
    library(Seurat)
    seurat_obj <- readRDS("/group/sms029/Oliva_dataset/integrated_col_trajectories.rds")
    ```
    

If you have trouble with the interactive approach, you could also create a SLURM script for batch processing of your Seurat object.

Remember to exit your interactive session when you're done to free up resources for other users.