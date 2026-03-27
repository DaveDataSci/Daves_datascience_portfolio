# Daves_datascience_portfolio
My Data Science Portfolio
#My_regreaaion_script_with_R
setwd(dirname(file.choose()))
getwd()
Obesedata <- read.csv("ObesityDataSet_raw_and_data_sinthetic.csv", stringsAsFactors = FALSE)
head(Obesedata)
tail(Obesedata)
str(Obesedata)
apply(Obesedata, MARGIN = 2, FUN = function(x) sum(is.na(x)))
library(Amelia)
missmap(Obesedata, col = c("black", "grey"), legend = FALSE)
summary(Obesedata)
library(ggplot2)

# Loop through each column in obesedata
for (var in colnames(Obesedata)) {
 
  # Skip non-numeric columns
  if (!is.numeric(Obesedata[[var]])) next
 
  x <- Obesedata[[var]]
 
  # -----------------------------
  # Histogram with density curve
  # -----------------------------
  png(paste0(var, "_histogram_density.png"))
  hist(
    x,
    main = paste("Histogram of", var),
    xlab = var,
    col = "lightblue",
    border = "black",
    prob = TRUE
  )
  lines(
    density(x, na.rm = TRUE),
    col = "red",
    lwd = 2
  )
  dev.off()
 
  # -----------------------------
  # Box Plot
  # -----------------------------
  png(paste0(var, "_boxplot.png"))
  boxplot(
    x,
    main = paste("Box Plot of", var),
    ylab = var,
    col = "lightgreen"
  )
  dev.off()
 
  # -----------------------------
  # Q-Q Plot
  # -----------------------------
  png(paste0(var, "_qqplot.png"))
  qqnorm(
    x,
    main = paste("Q-Q Plot of", var)
  )
  qqline(
    x,
    col = "red"
  )
  dev.off()
}
normality_results <- data.frame(variable = character(), n = integer(),
                                shapiro_stat = numeric(), shapiro_p = numeric(),
                                ks_stat = numeric(), ks_p = numeric(),
                                stringsAsFactors = FALSE)

setwd(dirname(file.choose()))
cat("Working directory:", getwd(), "\n")
Obesedata <- read.csv("ObesityDataSet_raw_and_data_sinthetic.csv", stringsAsFactors = FALSE)
cat("Data loaded: rows =", nrow(Obesedata), "cols =", ncol(Obesedata), "\n")
cols_to_include <- c(
  "NObeyesdad", "Age", "Height",
  "Weight", "family_history_with_overweight", "FAVC",
  "FCVC", "NCP", "CAEC",
  "SMOKE", "CH2O", "SCC",
  "FAF", "TUE", "CALC",
  "MTRANS"
)
cols_to_use <- cols_to_include[cols_to_include %in% colnames(Obesedata)]
Obesedata <- Obesedata[, cols_to_use, drop = FALSE]
rm(cols_to_include, cols_to_use)

# 3. ----- Clean column names and coerce numeric-like to numeric -----
colnames(Obesedata) <- gsub("[^A-Za-z0-9_]", "_", colnames(Obesedata))
colnames(Obesedata) <- tolower(colnames(Obesedata))
cat("Columns kept:", paste(colnames(Obesedata), collapse = ", "), "\n")

# try to convert non-numeric columns to numeric when possible
for (nm in names(Obesedata)) {
  if (!is.numeric(Obesedata[[nm]])) {
    tmp <- suppressWarnings(as.numeric(as.character(Obesedata[[nm]])))
    if (sum(is.finite(tmp), na.rm = TRUE) > 0) {
      Obesedata[[nm]] <- tmp
      message("Converted to numeric: ", nm)
    }
  }
}

# Identify numeric columns
numeric_cols <- sapply(Obesedata, is.numeric)
numeric_names <- names(Obesedata)[numeric_cols]
if (length(numeric_names) == 0) stop("No numeric columns detected in obesedata.")
cat("Numeric variables:", paste(numeric_names, collapse = ", "), "\n")

# 4. ----- Create folders -----
dir_create_safe <- function(path) {
  if (!dir.exists(path)) dir.create(path, recursive = TRUE)
  return(normalizePath(path))
}
base_plots_dir <- dir_create_safe(file.path(getwd(), "plots"))
dirs <- list(
  normality = dir_create_safe(file.path(base_plots_dir, "normality")),
  hist = dir_create_safe(file.path(base_plots_dir, "histograms")),
  hist_density = dir_create_safe(file.path(base_plots_dir, "histograms_density")),
  box = dir_create_safe(file.path(base_plots_dir, "boxplots")),
  qq = dir_create_safe(file.path(base_plots_dir, "qqplots")),
  correlation = dir_create_safe(file.path(base_plots_dir, "correlation")),
  partial = dir_create_safe(file.path(base_plots_dir, "partial_correlation")),
  scatter = dir_create_safe(file.path(base_plots_dir, "scatter_matrix")),
  pca = dir_create_safe(file.path(base_plots_dir, "pca")),
  kmo = dir_create_safe(file.path(base_plots_dir, "kmo_msa")),
  regression = dir_create_safe(file.path(base_plots_dir, "regression")),
  tests = dir_create_safe(file.path(base_plots_dir, "tests"))
)
cat("Created plot folders under:", base_plots_dir, "\n")

# helper to sanitize filenames
safe_name <- function(x) gsub("[^A-Za-z0-9_\\-\\.]", "_", x)

# helper to clean values (finite only)
clean_column <- function(x) x[is.finite(x)]

# 5. ----- Per-variable PNGs: histogram, histogram+density, boxplot, qqplot -----
library(ggplot2)

for (nm in numeric_names) {
  v <- clean_column(Obesedata[[nm]])
  fname_base <- safe_name(nm)
 
  # 5A histogram
  try({
    out <- file.path(dirs$hist, paste0("hist_", fname_base, ".png"))
    png(out, width = 1200, height = 900, res = 150)
    if (length(v) > 0) hist(v, main = paste("Histogram of", nm), xlab = nm, col = "lightblue", breaks = 20)
    else plot.new(); title(main = paste(nm, "- no finite values"))
    dev.off()
    cat("Saved:", out, "\n")
  }, silent = FALSE)
 
  # 5B histogram + density
  try({
    out <- file.path(dirs$hist_density, paste0("hist_density_", fname_base, ".png"))
    png(out, width = 1200, height = 900, res = 150)
    if (length(v) > 1) {
      hist(v, probability = TRUE, main = paste("Histogram + Density of", nm), xlab = nm, col = "lightblue", breaks = 20)
      lines(density(v), col = "red", lwd = 2)
    } else {
      plot.new(); title(main = paste(nm, "- insufficient data for density"))
    }
    dev.off()
    cat("Saved:", out, "\n")
  }, silent = FALSE)
 
  # 5C boxplot
  try({
    out <- file.path(dirs$box, paste0("boxplot_", fname_base, ".png"))
    png(out, width = 800, height = 900, res = 150)
    if (length(v) > 0) boxplot(v, main = paste("Boxplot of", nm), ylab = nm, col = "lightblue")
    else plot.new(); title(main = paste(nm, "- no finite values"))
    dev.off()
    cat("Saved:", out, "\n")
  }, silent = FALSE)
 
  # 5D Q-Q plot
  try({
    out <- file.path(dirs$qq, paste0("qq_", fname_base, ".png"))
    png(out, width = 800, height = 900, res = 150)
    if (length(v) >= 3) {
      qqnorm(v, main = paste("Q-Q plot of", nm)); qqline(v, col = "red")
    } else {
      plot.new(); title(main = paste(nm, "- <3 observations"))
    }
    dev.off()
    cat("Saved:", out, "\n")
  }, silent = FALSE)
}

# 6. ----- Normality tests (Shapiro-Wilk & KS) saved to CSV -----
normality_results <- data.frame(variable = character(), n = integer(),
                                shapiro_stat = numeric(), shapiro_p = numeric(),
                                ks_stat = numeric(), ks_p = numeric(),
                                stringsAsFactors = FALSE)
for (nm in numeric_names) {
  v <- clean_column(Obesedata[[nm]])
  n_obs <- length(v)
  sh_p <- NA; sh_stat <- NA; ks_p <- NA; ks_stat <- NA
  if (n_obs >= 3) {
    sh <- tryCatch(shapiro.test(v), error = function(e) NULL)
    if (!is.null(sh)) { sh_p <- sh$p.value; sh_stat <- as.numeric(sh$statistic) }
    ks <- tryCatch(ks.test(scale(v), "pnorm"), error = function(e) NULL)
    if (!is.null(ks)) { ks_p <- ks$p.value; ks_stat <- as.numeric(ks$statistic) }
  }
  normality_results <- rbind(normality_results,
                             data.frame(variable = nm, n = n_obs, shapiro_stat = sh_stat, shapiro_p = sh_p, ks_stat = ks_stat, ks_p = ks_p,
                                        stringsAsFactors = FALSE))
}
write.csv(normality_results, file = file.path(dirs$tests, "normality_shapiro_ks.csv"), row.names = FALSE)
cat("Saved normality tests:", file.path(dirs$tests, "normality_shapiro_ks.csv"), "\n")

# 7. ----- Correlation matrix & heatmap (single PNG) -----
library(corrplot)
cor_matrix <- cor(Obesedata[, numeric_names, drop = FALSE], use = "pairwise.complete.obs")
write.csv(cor_matrix, file = file.path(dirs$correlation, "correlation_matrix.csv"), row.names = TRUE)
png(file.path(dirs$correlation, "correlation_heatmap.png"), width = 1400, height = 1200, res = 150)
corrplot(cor_matrix, method = "color", type = "upper", order = "hclust", tl.cex = 0.7)
dev.off()
cat("Saved correlation heatmap and CSV\n")

# 8. ----- Scatterplot matrix: use GGally::ggpairs if not too many variables -----
if (!requireNamespace("GGally", quietly = TRUE)) install.packages("GGally")
library(GGally)
max_vars_for_pairs <- 12
if (length(numeric_names) <= max_vars_for_pairs) {
  out <- file.path(dirs$scatter, "scatterplot_matrix.png")
  png(out, width = 2200, height = 2200, res = 150)
  ggpairs(Obesedata[, numeric_names, drop = FALSE])
  dev.off()
  cat("Saved scatterplot matrix:", out, "\n")
} else {
  cat("Skipped scatterplot matrix (too many numeric variables > ", max_vars_for_pairs, ")\n")
}

# 9. ----- Partial correlation matrix & heatmap -----
if (!requireNamespace("ppcor", quietly = TRUE)) install.packages("ppcor")
library(ppcor)
partial_ok <- TRUE
pcor_res <- tryCatch(ppcor::pcor(Obesedata[, numeric_names, drop = FALSE]), error = function(e) { partial_ok <<- FALSE; NULL })
if (partial_ok && !is.null(pcor_res)) {
  partial_est <- pcor_res$estimate
  write.csv(partial_est, file = file.path(dirs$partial, "partial_correlation_matrix.csv"), row.names = TRUE)
  png(file.path(dirs$partial, "partial_correlation_heatmap.png"), width = 1400, height = 1200, res = 150)
  corrplot(partial_est, method = "color", type = "upper", order = "hclust", tl.cex = 0.7)
  dev.off()
  cat("Saved partial correlation heatmap and CSV\n")
} else {
  cat("Partial correlation failed or not calculable; check data (singular matrix / insufficient observations).\n")
}

# 10. ----- KMO & MSA -----
if (!requireNamespace("psych", quietly = TRUE)) install.packages("psych")
library(psych)
kmo_ok <- TRUE
kmo_res <- tryCatch(psych::KMO(cor_matrix), error = function(e) { kmo_ok <<- FALSE; NULL })
if (kmo_ok && !is.null(kmo_res)) {
  overall_kmo <- kmo_res$MSA
  per_item_msa <- kmo_res$MSAi
  write.csv(data.frame(variable = names(per_item_msa), msa = per_item_msa),
            file = file.path(dirs$kmo, "kmo_per_item.csv"), row.names = FALSE)
  sink(file.path(dirs$kmo, "kmo_results.txt")); print(kmo_res); sink()
  cat("Saved KMO results\n")
} else {
  cat("KMO computation failed (correlation matrix issues). Saved nothing for KMO.\n")
}

# 11. ----- PCA (mean impute, prcomp), scree + cumulative plots, rotated loadings -----
pca_input <- as.data.frame(lapply(Obesedata[, numeric_names, drop = FALSE], function(x) {
  xx <- as.numeric(x)
  if (all(!is.finite(xx))) return(xx)
  xx[!is.finite(xx)] <- mean(xx, na.rm = TRUE)
  xx
}))
# drop zero-variance columns
pca_input <- pca_input[, sapply(pca_input, function(x) var(x, na.rm = TRUE) > 0), drop = FALSE]
if (ncol(pca_input) < 1) stop("No variables left for PCA after filtering zero-variance.")
pca_res <- prcomp(pca_input, center = TRUE, scale. = TRUE)
eigvals <- pca_res$sdev^2
prop_var <- eigvals / sum(eigvals)
cum_var <- cumsum(prop_var)

# scree
png(file.path(dirs$pca, "pca_scree.png"), width = 1000, height = 800, res = 150)
plot(prop_var, type = "b", pch = 19, xlab = "Principal Component", ylab = "Proportion Variance", main = "PCA Scree Plot")
dev.off()
cat("Saved PCA scree\n")

# cumulative
png(file.path(dirs$pca, "pca_cumulative_variance.png"), width = 1000, height = 800, res = 150)
plot(cum_var, type = "b", pch = 19, xlab = "Principal Component", ylab = "Cumulative Variance", main = "PCA Cumulative Variance")
abline(h = c(0.5, 0.7, 0.8), col = "blue", lty = 2)
dev.off()
cat("Saved PCA cumulative variance\n")

# rotated loadings & interpretation
ncomp_k <- sum(eigvals > 1)
ncomp_c80 <- which(cum_var >= 0.8)[1]
ncomp <- if (!is.na(ncomp_k) && ncomp_k > 0) ncomp_k else if (!is.na(ncomp_c80)) ncomp_c80 else min(5, length(eigvals))
ncomp <- max(1, ncomp)
load_raw <- pca_res$rotation[, 1:ncomp, drop = FALSE]
if (ncol(load_raw) > 1) {
  rot <- varimax(load_raw)
  load_rot <- as.matrix(rot$loadings)
} else {
  load_rot <- load_raw
}
write.csv(load_rot, file = file.path(dirs$pca, "pca_rotated_loadings.csv"), row.names = TRUE)
# plain-language interpretation
interpret_list <- lapply(1:ncol(load_rot), function(i) {
  v <- load_rot[, i]; ord <- order(abs(v), decreasing = TRUE)
  topn <- names(v)[ord][1:min(6, length(v))]
  topl <- round(v[topn], 3)
  paste0("PC", i, " (", round(100*prop_var[i],1), "%): ", paste(sprintf("%s(%.3f)", topn, topl), collapse = ", "))
})
writeLines(unlist(interpret_list), file.path(dirs$pca, "pca_interpretation.txt"))
cat("Saved PCA loadings and interpretation\n")


# 13. ----- Regression pipeline: stepwise (AIC), LASSO, caret elastic-net -----
if (!requireNamespace("glmnet", quietly = TRUE)) install.packages("glmnet")
if (!requireNamespace("caret", quietly = TRUE)) install.packages("caret")
library(glmnet); library(caret); library(car)

# prepare model data with mean-imputation for predictors
num_data <- Obesedata[, numeric_names, drop = FALSE]
target_var <- if ("total_deaths" %in% names(num_data)) "total_deaths" else names(which.max(sapply(num_data, var, na.rm = TRUE)))
predictors <- setdiff(names(num_data), target_var)
if (length(predictors) == 0) {
  cat("No predictors available for regression. Skipping regression section.\n")
} else {
  model_df <- data.frame(y = num_data[[target_var]])
  for (p in predictors) {
    v <- as.numeric(num_data[[p]])
    v[!is.finite(v)] <- NA
    if (all(is.na(v))) next
    v[is.na(v)] <- mean(v, na.rm = TRUE)
    model_df[[p]] <- v
  }
  model_df <- model_df[, sapply(model_df, function(x) !all(is.na(x))), drop = FALSE]
  # split
  set.seed(2025)
  train_idx <- createDataPartition(model_df$y, p = 0.75, list = FALSE)
  train_df <- model_df[train_idx, , drop = FALSE]
  test_df <- model_df[-train_idx, , drop = FALSE]
 
  # stepwise OLS
  full_fm <- as.formula(paste("y ~", paste(setdiff(names(train_df), "y"), collapse = "+")))
  lm_null <- lm(y ~ 1, data = train_df)
  step_mod <- tryCatch(step(lm_null, scope = full_fm, direction = "both", trace = 0), error = function(e) lm_null)
  capture.output(summary(step_mod), file = file.path(dirs$regression, "stepwise_model_summary.txt"))
  cat("Saved stepwise model summary\n")
 
  # save regression diagnostics as PNG (multi-panel)
  png(file.path(dirs$regression, "regression_diagnostics.png"), width = 1600, height = 1200, res = 150)
  par(mfrow = c(2,2), mar = c(4,4,2,1))
  try(plot(step_mod, which = 1), silent = TRUE) # residuals vs fitted
  try(plot(step_mod, which = 2), silent = TRUE) # qq
  try(plot(step_mod, which = 3), silent = TRUE) # scale-location
  try(plot(step_mod, which = 4), silent = TRUE) # cooks
  dev.off()
  cat("Saved regression diagnostics PNG\n")
 
  # LASSO
  x_train <- as.matrix(train_df[, setdiff(names(train_df), "y"), drop = FALSE])
  y_train <- train_df$y
  x_test <- as.matrix(test_df[, setdiff(names(test_df), "y"), drop = FALSE])
  y_test <- test_df$y
  set.seed(123)
  cv_lasso <- cv.glmnet(x_train, y_train, alpha = 1, nfolds = 10, standardize = TRUE)
  best_lambda <- cv_lasso$lambda.min
  lasso_fit <- glmnet(x_train, y_train, alpha = 1, lambda = best_lambda, standardize = TRUE)
  coef_lasso <- as.matrix(coef(lasso_fit))
  write.csv(coef_lasso, file = file.path(dirs$regression, "lasso_coefficients.csv"), row.names = TRUE)
  cat("Saved LASSO coefficients\n")
  # LASSO predictions performance
  pred_lasso <- predict(lasso_fit, newx = x_test)
  rmse_lasso <- sqrt(mean((as.numeric(pred_lasso) - y_test)^2, na.rm = TRUE))
  r2_lasso <- if (length(pred_lasso) > 1) cor(as.numeric(pred_lasso), y_test)^2 else NA
  capture.output(c(rmse = rmse_lasso, r2 = r2_lasso), file = file.path(dirs$regression, "lasso_performance.txt"))
  cat("Saved LASSO performance\n")
 
  # caret elastic-net
  set.seed(321)
  train_control <- trainControl(method = "repeatedcv", number = 5, repeats = 3)
  caret_train_df <- data.frame(y = y_train, x_train)
  caret_model <- train(y ~ ., data = caret_train_df, method = "glmnet",
                       trControl = train_control, tuneLength = 8)
  saveRDS(caret_model, file = file.path(dirs$regression, "caret_elasticnet_model.rds"))
  cat("Saved caret elastic-net model RDS\n")
  pred_caret <- predict(caret_model, newdata = data.frame(x_test))
  rmse_caret <- sqrt(mean((pred_caret - y_test)^2, na.rm = TRUE))
  r2_caret <- if (length(pred_caret) > 1) cor(pred_caret, y_test)^2 else NA
  capture.output(c(rmse = rmse_caret, r2 = r2_caret), file = file.path(dirs$regression, "caret_performance.txt"))
  cat("Saved caret performance\n")
 
  # save performance summary
  perf_summary <- data.frame(model = c("LASSO", "Caret_ElasticNet"),
                             RMSE = c(rmse_lasso, rmse_caret),
                             R2 = c(r2_lasso, r2_caret))
  write.csv(perf_summary, file = file.path(dirs$regression, "model_performance_summary.csv"), row.names = FALSE)
  cat("Saved model performance summary\n")
}

# 14. ----- Final message with produced files -----
cat("\nAll requested outputs have been created under the 'plots' folder in your working directory.\n")
cat("Top-level folders created:\n")
print(names(dirs))
cat("\nIf you want any further customization (different k for kmeans, ggplot styling, larger PNG resolution), tell me the change and I'll update the script.\n")

library(rcompanion)
library(nortest)
Obesedata$age <- transformTukey(Obesedata$age)
Obesedata$height <- transformTukey(Obesedata$height)
Obesedata$weight <- transformTukey(Obesedata$weight)

set.seed(12345)
Obesedata <- Obesedata[order(runif(506)), ]

#split data set
Obesedata.tr <- Obesedata[1:354, ]     # 70%
Obesedata.te <- Obesedata[355:506, ]   # 30%

# Linear Regression
model1 <- lm(age ~ height, data = Obesedata.tr) #linear regression model between age and height variable
model2 <- lm(age ~ weight, data = Obesedata.tr) #linear regression model between age and weight variable

# Add regression line to scatter plot between age and height variable
plot(Obesedata.tr$age, Obesedata.tr$height, main = "Scatterplot",
     xlab = "lower status", ylab = "transform median value")
abline(model1, col="red")

# Add regression line to scatter plot between age and weight variable
plot(Obesedata.tr$age, Obesedata.tr$weight, main = "Scatterplot",
     xlab = "lower status", ylab = "transform median value")
abline(model1, col="red")

# Summarizing the models
summary(model1)

#converting nobeyesdad to numeric so as to run linear regression model
Obesedata$nobeyesdad_num <- as.numeric(as.factor(Obesedata$nobeyesdad))
model3 <- lm(nobeyesdad_num ~ age + height + weight, data = Obesedata)
summary(model3)
