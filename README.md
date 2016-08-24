# docker-PostgreSQL-Hunspell
Docker File for creating a en-us Hunspell enabled PostgreSQL instance for FTS

[Pull from Docker Hub](https://hub.docker.com/r/tecktron/postgresql-hunspell-en-us/)

This demonstrates how easy it is to add the Hunspell dictionaries into a PosgreSQL instance for use with full text searching (FTS).

This image uses the official PostgreSQL image and therefore works in the exact same way. For more information on that please see the [offical Docker page](https://hub.docker.com/_/postgres/) or the [official Git repo](https://github.com/docker-library/postgres).

To setup the Hunspell dictionaries, you can use something like the following SQL commands:
(Please note this is just my suggestion, and you should tailor this to suit your own needs) 

```
-- install the required extensions
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE EXTENSION IF NOT EXISTS unaccent;

-- simply remove stop words and continue
CREATE TEXT SEARCH DICTIONARY english_simple (
	template = simple,
	StopWords = english,
	accept = false
);

-- this setups the dictionary for ispell searching
CREATE TEXT SEARCH DICTIONARY english_ispell (
	template = ispell,
	DictFile = english, -- if we didn't update the links these two
	AffFile = english,  -- would have to be en_us instead
	StopWords = english
);

CREATE TEXT SEARCH DICTIONARY english_stem (
	template = snowball,
	language = english
);

-- alter the config to use the ispell and stem (snowball) dictionaries
ALTER TEXT SEARCH CONFIGURATION	pg_catalog.english ALTER MAPPING
FOR asciiword, asciihword, hword_asciipart, word, hword, hword_part
WITH unaccent, english_simple, english_ispell, english_stem;
```


For more information on the power and use of PostgreSQL FTS, I suggest you read this simply wonderful 3 part tutorial: [PostgreSQL: A full text search engine](http://shisaa.jp/postset/postgresql-full-text-search-part-1.html)


