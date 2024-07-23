# University Units
```dataview
table without id file.link as Title, Priority, Week, Lecture, Flashcards
from !"git-repo/Templates"
where Class = "University"
where Status != "To do"
where Status != "Done"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```

# University Completed
```dataview
table without id file.link as Title, Status, Priority, Lecture, Flashcards
from !"Templates"
where Class = "University"
where Status != "Working"
where Status != "Done"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```

# Personal Projects
```dataview
table without id file.link as Title, Status, Priority
from !"git-repo/Templates"
where Class = "Personal"
where Status != "To do"
where Status != "Done"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```

# Personal Completed
```dataview
table without id file.link as Title, Status, Priority
from !"Templates"
where Class = "Personal"
where Status != "To do"
where Status != "Working"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```

# Work Projects
```dataview
table without id file.link as Title, Status, Priority
from !"git-repo/Templates"
where Class = "Work"
where Status != "To do"
where Status != "Done"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```

# Work Completed
```dataview
table without id file.link as Title, Status, Priority
from !"Templates"
where Class = "Work"
where Status != "To do"
where Status != "Working"
sort choice(Priority = "High", "1", choice(Priority = "Medium", "2", choice(Priority = "Low", "3","4"))) asc
sort choice(Status = "Edit", "1", choice(Status = "Outline", "2","3")) asc
```