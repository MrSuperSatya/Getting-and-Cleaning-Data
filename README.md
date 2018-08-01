How the script works:

### 1. Merges the training and the test sets to create one data set.
	library(dplyr)
	df_X_train      <- read.table('X_train.txt')
	df_X_test       <- read.table('X_test.txt')
	df_y_train      <- read.table('y_train.txt')
	df_y_test       <- read.table('y_test.txt')
	subject_train   <- read.table('subject_train.txt')
	subject_test    <- read.table('subject_test.txt')
	features        <- read.table('features.txt')
	activity_labels <- read.table('activity_labels.txt')

	df_train <- cbind(df_X_train, subject_train, df_y_train)
	df_test  <- cbind(df_X_test, subject_test, df_y_test)
	df <- rbind(df_train, df_test)

### 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
	apply(df[,1:561], 2, mean)
	apply(df[,1:561], 2, sd)

### 3. Uses descriptive activity names to name the activities in the data set
	df[, 563] <- factor(df[, 563], levels = activity_labels[,1], labels = activity_labels[, 2])

### 4. Appropriately labels the data set with descriptive variable names.
	column_names <- c(as.character(features[, 2]), 'subject', 'activity_label')
	column_names <- paste('var', c(1:563), column_names, sep='_')
	colnames(df) <- column_names

### 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
	df_summ <- df %>% group_by(var_562_subject, var_563_activity_label) %>% summarise_all(funs(mean))
	df_summ <- df_summ[, c(3:563, 1, 2)]
	write.table(df_summ, file='tidy_data_set_step5.txt', row.names = FALSE)
	write.csv(df_summ, file = 'tidy_data_set_step5.csv', row.names = FALSE, col.names = FALSE)
