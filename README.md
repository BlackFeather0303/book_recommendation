# book_recommendation
This is a book recommendation system with tensorflow and python3.6

## DataSet
数据集使用的是goodbooks-10k数据集
该数据集包含了对10k最受欢迎的数据的六百万条评论，同时也包括了：
- books marked to read by the users
- book metadata(author, year, etc.)
- tags/shelves/genres
数据源来自：https://github.com/zygmuntz/goodbooks-10k
由于数据集过大，无法置于GitHub，所以全部数据集需要在以下link下载：
https://github.com/zygmuntz/goodbooks-10k/releases

href="https://github.com/zygmuntz/goodbooks-10k/releases/download/v1.0/goodbooks-10k.zip"

### DataSet Contents
**ratings.csv** contains ratings sored by time.(user_id, book_id, rating)
Ratings go from one to five. Both book IDs and user IDs are contiguous. For books, they are 1-10000, for users, 1-53424.

**to_read.csv** provids IDs of books marked "to read" by each user, sorted by time, There are close to a million pairs

**books.csv** has metadata for each book (goodreads IDs, authors, title, average rating, etc) The metadata have been extracted from goodreads XML file, availiable in books_xml

**book_tags.csv** contains tags/shelves/genres assigned by users to books. Tags in this file are represented by their IDs, They are sorted by goodreads_book_id ascending and count descending.
Here, each tag/shelf is given an ID, tags.csv translates tag IDs to names

**goodreads IDs** Each book may have many edition. goodreads_book_id and best_book_id generally point to the most popular edition of a  given book, while goodreads work id refers to the book in the abstract sense.

##### data details

- book_tags: goodreads_book_id, tag_id, count
- books: book_id, goodreads_book_id, best_book_id, work_id, books_count, isbn,
		isbn13, authors, original_publication_year, original_title, ratings_count,
		working_ratings_count work_text_review_count, ratings_1, ratings_2, ratings_3,
		ratings_4, ratigns_5, image_url, small_image_url
- ratings: user_id, book_id, ratings
- tags: tag_id, tag_name
- to_read: user_id, book_id

### 数据使用
在数据集中，有很多title 。通过分析，我们使用如下titles as features
1. book: author, original_title, tags
2. user: user_id,
3. rating: ratings

### 关于数据的疑问！！！！！
1. goodreads_book_id和best_book_id是什么
2. ratings_count 和 work_ratings_count有什么区别

## 数据下载和解压 data_download.py


## 数据处理 data_processing.py
首先将无用的数据title过滤，然后将各个表通过外键连接起来。
一共有三个实体：1 book，2 reader，3 rating
1. book保留特征：book_id, title, authors, Tags
2. user保留特征：user_id
3. rating为user对书籍的评分，作为监督学习最后的标签。 同时，考虑到有to_read表，表示user想读的书籍，该特征也可以作为标签的一种更好的结合到一起。
#### book_processing函数
传入所有和book相关的数据，输出book特征处理后的数据
1. 过滤books表，保留：book_id|goodreads_book_id|best_book_id|authors|original_title|ratings_count|ratings_1|ratings_2|ratings_3|ratings_4|ratings_5
	1. 考虑到best_book_id 等于 goodreads_book_id并且后者只在后期合并表时有意义，所以删掉best_book_id
	2. 对author和original_title作word2int，转换为int便于之后的embedding
	3. 记录每个书籍的平均得分0～5
2. 将book表和book_tags表合并，得到带有书籍风格标签的表（暂时将tag表保留，不进行表合并）
3. 将books, authors2int, title2int, book_tags, tags，保存为 book_params.p文件
4. 其中books的格式为：[book_id:int, goodreads_book_id:int, authors:int, original_title:int, ratings_1:int]



## 书籍特征处理 book_feature_processing.py
使用tensorflow对书籍相关的所有特征进行表示


## 用户特征处理 user_feature_processing.py
