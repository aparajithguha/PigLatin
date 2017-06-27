# Movie Analysis using Pig
This is a basic project made with PigLatin scripts to perform a movie analysis along with Read, write operations in various formats.


Load the data
--------------
<code>movies = load 'movie.txt' using PigStorage(',') as (id:int, title:chararray, year:int, rating:float, views:int)</code>

Splitting movies based on low and high rated
---------------------------------------------

<code>SPLIT movies into lowrated if rating<3.0f, highrated if rating>3.0f;</code>

COUNT the low and high rated movies
------------------------------------

<pre>grouplow = group lowrated all;
grouphigh = group highrated all;

lowratedcount = foreach grouplow generate COUNT(lowrated.id);
dump lowratedcount;

highratedcount = foreach grouphigh generate COUNT(highrated.id);
dump highratedcount;</pre>

avg movie views
------------------
<pre>groupmovies = group movies all;
avgmovies = foreach groupmovies generate AVG(movies.views)
dump avgmovies</pre>

max viewed low rated movie
---------------------------
<code>maxview = foreach grouplow generate MAX(lowrated.title),MAX(lowrated.views)</code>

min viewed high rated movie
---------------------------
<code>minview = foreach grouphigh generate min(highrated.title),min(highrated.views)</code>

low rated movies with average rating by year
--------------------------------------------

<pre>groupyear = group lowrated by year ASC;

maxyear4 = foreach groupyear generate group as release, COUNT(lowrated.rating) as count, AVG(lowrated.rating);</pre>

high rated movies by year
--------------------------

<pre>hgroupyear = group highrated by year DESC;

hyear = foreach hgroupyear generate group as relase, COUNT(highrated.rating) as count;</pre>


store data using JSON and PIG and Avro
---------------------------------------

<pre>store lowrated into 'json' USING JsonStorage();
store highrated into 'jsonh' USING JsonStorage();

store lowrated into 'avro' USING AvroStorage();
store highrated into 'havro' USING AvroStorage();

store maxyear4 into 'text' USING PigStorage(',')</pre>
