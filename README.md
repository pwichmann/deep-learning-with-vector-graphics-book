# "Deep Learning with Vector Graphics" Book

[Please find the 'live' interactive book here](https://pwichmann.github.io/deep-learning-with-vector-graphics-book)


## How To publish the book

* Build the book using Jupyter Book
	* conda activate book
	* cd ~/Documents/01_quiver/00_book/deep-learning-with-vector-graphics-book
	* jupyter-book build quiver_book
* Then copy the content of the build/HTML folder into the root/docs folder
* Make sure to keep the (hidden) .nojekyll file in the folder
* Then push to main