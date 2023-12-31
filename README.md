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
where, nag is the number of books written by author a of genre g.
</br></br>
The author A1 would have also written several other books B121, B131, and more, of other genres G2, G3, and so on. 
Similarly, there will be several other books B211, B311, and more, of genre G1 which have been written by other authors A2, A3, and so on.
Hence, in order to compute the probability of seeing an A1G1 pair in the dataset, I will calculate a Joint Probability, which will be the product of 2 probabilities (assuming the 2 probabilities as being independent of each other) –

1.	PAG(a,g): Probability of finding a book written by an author a which has the genre g. 
Let the, 
- number of books written by author A1 of genre G1 be C11, and 
- total number of books written by author A1 be CA1. 
Then,
</br><img src="/Equations/eq3.png"></br>
Therefore, in general - 
</br><img src="/Equations/eq4.png"></br></br>
2.	PGA(g,a): Probability of finding a book of genre G1 which is written by author A1.
Let the, 
- number of books of genre G1 written by author A1 be C11, and
- total number of books of genre G1 be CG1.
Then,
</br><img src="/Equations/eq5.png"></br>
Therefore, in general - 
</br><img src="/Equations/eq6.png"></br>
Thus, the Joint Probability of seeing a particular author-genre pair in the dataset is – 
</br><img src="/Equations/eq7.png"></br>
<img src="/Equations/eq8.png"></br>
The weight of a particular author-genre pair will be directly proportional to the rating given to it and will also be directly proportional to the probability of its occurrence, so the overall weight will be a product of these two – 
<img src="/Equations/eq9.png"></br>
<img src="/Equations/eq10.png"></br>

### Author Genre Association Module
This module performs community detection and analysis on the bipartite graph of 1% sample of the dataset, representing relationships between authors and genres in the dataset. The resulting communities, link weights, and common genres are visualized and saved for further analysis.

1.	Loading, visualizing and analyzing the 1% Sample Bipartite Graph:
-	The code reads a previously saved 1% sample bipartite graph from a pickle file ("1%_Sample_author_genre_bipartite.gpickle") using NetworkX.
-	The bipartite graph is visualized using matplotlib. 
-	Nodes are categorized into genres and authors based on their bipartite attribute.
-	The unique genres and authors are printed along with their counts.

2.	Projecting Bipartite Graph onto Authors:
-	The bipartite graph is projected onto the set of authors to create a new graph (`sample_author_projection`).
-	Degree centrality is calculated for authors.

3.	Community Detection with Louvain Algorithm:
-	Louvain community detection algorithm is applied to identify communities within the projected graph.
-	Degree centrality and associated genres are printed for each author.

4.	Visualizing the Author Projection:
-	The projected graph is visualized using matplotlib, showing connections between authors.

5.	Weight Calculation for Author-Author Edges:
-	Weights are calculated for each author-author edge in the projected graph based on common genres and their weights in the original bipartite graph.

6.	Creating a Communities Network:
-	A new graph (`C`) is created to represent the communities, including edges with the computed weights.
-	The community network is visualized using matplotlib.

7.	Data Analysis for Communities:
-	Data is collected for each author-author edge in the community network, including community ID, link weight, and common genres.
-	The data is stored in a DataFrame (`communities_df`) and printed.

8.	Community Filtering and Visualization:
-	A new community network (`C1`) is created by excluding inter-community edges.
-	The filtered community network is visualized, and the data is collected and stored in a new DataFrame (`communities_df2`).

9.	Saving Community Data:
-	The final community data is saved to a CSV file ("Communities_1%_BookData.csv").

### Author-Author Edge Weight Computation
Let there be authors A1 and A2 in the bipartite graph. A1 is connected to genres G1, G2 and G3, while A2 is connected to genres G1, G3, and G4. Then in the author-projected graph there will be an <A1,A2> edge because of genres G1 and G3. 

Hence, the weight of this edge in the projected network is defined as such, 
<img src="/Equations/eq11.png"></br>
Therefore, in general – 
<img src="/Equations/eq12.png"></br>
where, 
- n is the number of common genres between authors x and y, and
- W(x,Gk) is the weight of the edge between author x and genre Gk in the bipartite graph, similarly W(y,Gk) is the weight of the edge between author y and genre Gk in the bipartite graph.

### Recommendation Module
This module provides an interactive book recommendation system based on the user's input, leveraging community data from previously analyzed graph. It allows the user to interactively search for a book and receive recommendations for similar books. The recommendations are influenced by the community structure, suggesting books by authors within the same community as the selected book's author.
1.	Loading the Dataset and Creating a Sample:
-	The code loads a processed book dataset from "Processed_BookData.csv" using pandas.
-	A random 1% sample of the dataset (`df_sample`) is created for testing.
2.	Loading Communities Data:
-	The code loads a CSV file containing community data ("Communities_1%_BookData.csv") that was previously generated.
3.	Interactive Book Search:
-	The user is presented with a list of books to choose from (output of `showBooks()` function).
-	The user is prompted to enter a book title and the number of book recommendations to get.
4.	SearchBook Function:
-	The user's input (book title and number of recommendations) is taken.
-	The details of the selected book are displayed, including author, number of pages, rating, rating count, and genres.
-	The function `get_all_authors` retrieves other authors from the same community as the selected book's author.
-	The function `get_n_books` provides book recommendations based on the authors obtained from the community.
-	The recommendations are displayed, including the book title and author.
5.	User Interaction:
-	The user can input the book title and the number of recommendations they want to receive.

Output:
-	The code outputs details about the selected book and a list of recommended books along with their authors.
