# Genre-Centric_Book-Recommendation_System
In this project a comprehensive Book-dataset is analyzed to uncover the relationships between authors, genres, and reader preferences. Through advanced analytics, author-genre associations and genre-based author communities are identified, and a robust recommendation system that suggests books to readers is developed based on the communities.

## Dataflow diagram of the system
<img src="/SIN Project DFD.jpeg"></br></br>

## Implementation of Modules
### Data Preprocessing Module 
This module cleans and preprocesses a dataset of book data, handling missing values, converting data types, filtering out non-English content, and processing genres before saving the processed data to a new CSV file.
1.	Loading the Dataset:
-	The code begins by importing necessary libraries, such as pandas and numpy.
-	It loads a dataset from a CSV file ("My_Book_data.csv") into a Pandas DataFrame (df).
-	Initial exploratory data analysis is done using `head()`, `shape`, `size`, `info()`, and `columns` to understand the structure and content of the dataset.

2.	Filtering Columns:
-	The code defines a list of columns to keep (`filter_cols`) and removes the columns not in this list from the DataFrame.
-	Unimportant columns such as book_description, book_edition, book_format, book_isbn, image_url are filtered out.
-	The resulting DataFrame (`df_filtered`) is displayed, and its information is printed.

3.	Handling Missing Values: 
-	Rows with missing values are removed from `df_filtered` using `dropna()`.

4.	Processing 'book_pages' Column:
-	The 'book_pages' column, initially of object type, is converted to integers after removing the "pages" suffix from each element.

5.	Handling Multiple Authors:
-	Some cells in the 'book_authors' column have multiple authors separated by "|". The code defines a function (`removeMultipleAuthors`) to remove rows with multiple authors.

6.	Language Filtering:
-	Two functions (`is_english` and `removeOtherLangs`) are defined to filter out rows where 'book_authors' or 'book_title' are not in English.
-	Rows with non-English content are removed from the DataFrame.

7.	Genres Processing:
-	The 'genres' column, initially containing pipe-separated strings, is converted to lists using the `changeDelimiter` function.
-	Duplicate genres within each row are removed, and the lists are sorted.
-	The processed lists are converted back to strings and assigned to the 'genres' column.

8.	Saving Processed DataFrame:
-	The processed DataFrame is saved to a new CSV file ("Processed_BookData.csv") using `to_csv()`.

9.	Final Output:
-	The code displays a subset of the processed DataFrame for examination (`df_filtered[1:len(df_filtered):2000]`).

### Network Construction Module
This module focuses on creating and analyzing bipartite graphs for 1% and 10% samples of the processed book dataset. It calculates weights based on various metrics and visualizes the resulting graphs, providing insights into the relationships between authors and genres in the sampled data.
1.	Loading the Processed Dataset:
-	The code begins by importing necessary libraries: pandas, networkx, matplotlib, random, and pickle.
-	It loads a previously processed dataset from a CSV file ("Processed_BookData.csv") into a Pandas DataFrame (df).

2.	Identifying Unique Genres:
-	The code extracts unique genres from the 'genres' column in the DataFrame, creating a set (`genreList`).
-	The total number of unique genres is printed.

3.	Sampling 1% of the Dataset:
-	A 1% sample of the dataset (`df_1_sample`) is created for testing purposes using random sampling.
-	The genres column is transformed into a list of genres.

4.	Creating a Bipartite Graph for 1% Sample:
-	A bipartite graph (`B_1_sample`) is created using NetworkX.
-	Nodes are added for authors and genres.
-	Edges are added with weights calculated based on specified metrics, including ratings, rating counts, review counts, and probabilities.
-	The resulting graph is saved using the `writeGraph` function.

5.	Visualizing the 1% Sample Bipartite Graph:
-	The code visualizes the bipartite graph using matplotlib, with nodes representing authors and genres, and edges representing connections between them. Weights are displayed on the edges.

6.	Sampling 10% of the Dataset:
-	A 10% sample of the dataset (`df_10`) is created for further analysis using random sampling.
-	Similar to the 1% sample, the genres column is transformed into a list of genres.

7.	Creating a Bipartite Graph for 10% Sample:
-	A new bipartite graph (`B_10`) is created for the 10% sample.
-	Nodes, edges, and weights are added similar to the process for the 1% sample.
-	The resulting graph is saved using the `writeGraph` function.

8.	Visualizing the 10% Sample Bipartite Graph:
-	The code visualizes the bipartite graph for the 10% sample using matplotlib, with nodes representing authors and genres, and edges representing connections between them. Weights are displayed on the edges.

### Author-Genre Edge Weight Computation
- Say there is an author A1 who has written books B111, B112, and B113 of genre G1.  
- The bipartite graph will therefore include an edge <A1,G1>. 
- In the dataset each book has a book rating, book rating count and book review count. In order to estimate the overall rating that people would give for the A1-G1 pair, I will compute the weighted average of the book ratings for B111, B112 and B113, where the weights will be the averages of the corresponding book rating counts and book review counts.
<table>
  <tr>
    <th>Book</th>
    <th>Book Rating</th>
    <th>Book Rating Count</th>
    <th>Book Review Count</th>
    <th>Book Weight</th>
  </tr>
  <tr>
    <td>B111</td>
    <td>R111</td>
    <td>RC111</td>
    <td>ReC111</td>
    <td>BW111 = (RC111+ReC111)/2</td>
  </tr>
  <tr>
    <td>B112</td>
    <td>R112</td>
    <td>RC112</td>
    <td>ReC112</td>
    <td>BW112 = (RC112+ReC112)/2</td>
  </tr>
  <tr>
    <td>B113</td>
    <td>R113</td>
    <td>RC113</td>
    <td>ReC113</td>
    <td>BW113 = (RC113+ReC113)/2</td>
  </tr>
</table>
<img src="/Equations/eq1.png"></br>
Therefore, in general - 
<img src="/Equations/eq2.png"></br>
where, _nag is the number of books written by author a of genre g.

The author A1 would have also written several other books B121, B131, and more, of other genres G2, G3, and so on. 
Similarly, there will be several other books B211, B311, and more, of genre G1 which have been written by other authors A2, A3, and so on.
Hence, in order to compute the probability of seeing an A1G1 pair in the dataset, I will calculate a Joint Probability, which will be the product of 2 probabilities (assuming the 2 probabilities as being independent of each other) –
1.	PAG(a,g): Probability of finding a book written by an author a which has the genre g. 
Let the, 
•	number of books written by author A1 of genre G1 be C11, and 
•	total number of books written by author A1 be CA1. 
Then,
