# Programming Assignment 2: Lexical Scoping

## For the Coursera course [R Programming](https://www.coursera.org/course/rprog)

The following code demonstrates how to use the `assessment3.R` R script.

Lines starting with `#` are simple comments, lines starting with `#>` are things
printed in the output.

	# read the R script
	# replace the "path/to/file" with the directory you save the file into
	# or you can read the file directly from the web
	source("path/to/file/assessment3.R")

	# create a *square* matrix (because `solve` only handles square matrices)
	# create the matrix during the call of makeCacheMatrix()
	a <- makeCacheMatrix( matrix(c(1,2,12,13), nrow = 2, ncol = 2) );

	summary(a);
	#>              Length Class  Mode    
	#> setMatrix    1      -none- function
	#> getMatrix    1      -none- function
	#> cacheInverse 1      -none- function
	#> getInverse   1      -none- function

	a$getMatrix();
	#>      [,1] [,2]
	#> [1,]    1   12
	#> [2,]    2   13

	cacheSolve(a)
	#> [,1]        [,2]
	#> [1,] -1.1818182  1.09090909
	#> [2,]  0.1818182 -0.09090909

	# the 2nd time we run the function,we get the cached value
	cacheSolve(a)
	#> getting cached data
	#> [,1]        [,2]
	#> [1,] -1.1818182  1.09090909
	#> [2,]  0.1818182 -0.09090909

Alternatively, the matrix can be created after calling a `makeCacheMatrix`
without arguments.

	# read the R script
	# replace the "path/to/file" with the directory you save the file into
	# or you can read the file directly from the web
	source("path/to/file/assessment3.R")

	# call makeCacheMatrix without arguments
	a <- makeCacheMatrix();
	summary(a);
	#>              Length Class  Mode    
	#> setMatrix    1      -none- function
	#> getMatrix    1      -none- function
	#> cacheInverse 1      -none- function
	#> getInverse   1      -none- function

	# create a square matrix (reason `solve` only handles square matrices )
	a$setMatrix( matrix(c(1,2,12,13), nrow = 2, ncol = 2) );
	a$getMatrix();
	#>      [,1] [,2]
	#> [1,]    1   12
	#> [2,]    2   13

	cacheSolve(a)
	#> [,1]        [,2]
	#> [1,] -1.1818182  1.09090909
	#> [2,]  0.1818182 -0.09090909

	# the 2nd time we run the function, we get the cached value
	cacheSolve(a)
	#> getting cached data
	#> [,1]        [,2]
	#> [1,] -1.1818182  1.09090909
	#> [2,]  0.1818182 -0.09090909

 69  assessment3.R 
@@ -0,0 +1,69 @@
# See README.md for instructions on running the code and output from it
# The assignment states that running the code is not part of the grading 
# but I have the instructions anyway.

# makeCacheMatrix is a function that returns a list of functions
# Its puspose is to store a martix and a cached value of the inverse of the 
# matrix. Contains the following functions:
# * setMatrix      set the value of a matrix
# * getMatrix      get the value of a matrix
# * cacheInverse   get the cahced value (inverse of the matrix)
# * getInverse     get the cahced value (inverse of the matrix)
#
# Notes:
# not sure how the "x = numeric()" part works in the argument list of the 
# function, but it seems to be creating a variable "x" that is not reachable 
# from the global environment, but is available in the environment of the 
# makeCacheMatrix function
makeCacheMatrix <- function(x = numeric()) {

        # holds the cached value or NULL if nothing is cached
        # initially nothing is cached so set it to NULL
        cache <- NULL

        # store a matrix
        setMatrix <- function(newValue) {
                x <<- newValue
                # since the matrix is assigned a new value, flush the cache
                cache <<- NULL
        }

        # returns the stored matrix
        getMatrix <- function() {
                x
        }

        # cache the given argument 
        cacheInverse <- function(solve) {
                cache <<- solve
        }

        # get the cached value
        getInverse <- function() {
                cache
        }

        # return a list. Each named element of the list is a function
        list(setMatrix = setMatrix, getMatrix = getMatrix, cacheInverse = cacheInverse, getInverse = getInverse)
}


# The following function calculates the inverse of a "special" matrix created with 
# makeCacheMatrix
cacheSolve <- function(y, ...) {
        # get the cached value
        inverse <- y$getInverse()
        # if a cached value exists return it
