### APIs

To find papers under the search term of “sars-cov-2 trial vaccine,” the
following code was produced, and the number of results were counted:

    website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2+trial+vaccine")

    counts <- xml2::xml_find_first(website, "/html/body/main/div[9]/div[2]/div[2]/div[1]/div[1]/span")

    counts <- as.character(counts)

Based on the search term(s) “sars-cov-2 trial vaccine,” there were 2,336
papers found.

To download paper details for the first 250 papers and format the IDs
sourced from the code:

    query_ids <- GET(
          url   = "https://eutils.ncbi.nlm.nih.gov/",
          path = "entrez/eutils/esearch.fcgi",
          query = list(db       = "pubmed",
                       term     = "sars-cov-2 trial vaccine",
                       rettype = "abstract",
                       retmax   = 250)
        )

    ids <- httr::content(query_ids)
    ids <- as.character(ids)
    ids <- stringr::str_extract_all(ids, "<Id>[0-9]+</Id>")[[1]]
    ids <- stringr::str_remove_all(ids, "<Id>|</Id>")

To then create a dataset for the first 250 papers and their information
about ID, title, journal name, publication date, and abstract:

    publications <- GET(
      url = "https://eutils.ncbi.nlm.nih.gov/",
      path = "entrez/eutils/efetch.fcgi",
      query = list(
        db = "pubmed",
        id = paste(ids, collapse = ","),
        retmax = 250,
        rettype = "abstract"
        ))
    publications <- httr::content(publications)
    publications_txt <- as.character(publications)

    pub_char_list <- xml2::xml_children(publications)
    pub_char_list <- sapply(pub_char_list, as.character)
        
    # From Lab 7, and the other variables are modeled after the next three lines of code
    abstracts <- str_extract(pub_char_list, "<Abstract>[[:print:][:space:]]+</Abstract>")
    abstracts <- str_remove_all(abstracts, "</?[[:alnum:]- =\"]+>")
    abstracts <- str_replace_all(abstracts, "[[:space:]]+", " ")

    # For article titles...
    titles <- str_extract(pub_char_list, "<ArticleTitle>[[:print:][:space:]]+</ArticleTitle>")
    titles <- str_remove_all(titles, "</?[[:alnum:]- =\"]+>")
      
    # For journal titles...
    journals <- str_extract(pub_char_list, "<Title>[[:print:][:space:]]+</Title>")
    journals <- str_remove_all(journals, "</?[[:alnum:]- =\"]+>") 
    journals <- str_replace_all(journals, "[[:space:]]+", " ")
      
    # For publciation dates
    dates <- str_extract(pub_char_list, "<PubDate>[[:print:][:space:]]+</PubDate>")
    dates <- str_remove_all(dates, "</?[[:alnum:]- =\"]+>")
    dates <- str_replace_all(dates, "[[:space:]]+", " ")

    database <- data.frame(
          PubMedID = ids,
          Title = titles,
          Journal = journals,
          PubDate = dates,
          Abstract = abstracts
        )

    knitr::kable(database[1:20, ], caption = "Twenty papers about SARS-CoV-2 Trial Vaccines")

### Text Mining

To tokenize and count words from the dataset provided for this
assignment:

    # Read in data from provided repository
    gitData <- read.csv("pubmed.csv")

    # With stop words
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, sort = TRUE) %>%
      top_n(20) %>%
      ggplot(aes(x = n, y = fct_reorder(word,n))) +
      geom_col(color = "#1FA971", fill = "#6ACBA4") +
      labs(x = "Occurrences", y = "Word", title = "Top 20 Common Tokens (with stop words)")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-counts-1.png)

    # Without stop words
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, sort = TRUE) %>%
      anti_join(stop_words, by = "word") %>%
      filter(!grepl(pattern = "^[0-9]+$", x = word)) %>%
      top_n(20) %>%
      ggplot(aes(x = n, y = fct_reorder(word,n))) +
      geom_col(color = "#247EA2", fill = "#57ABCD") +
      labs(x = "Occurrences", y = "Word", title = "Top 20 Common Tokens (without stop words)")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-counts-2.png)

    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      anti_join(stop_words, by = "word") %>%
      group_by(term) %>%
      count(word, sort = TRUE) %>%
      top_n(5) %>%
      knitr::kable(caption = "Top 5 Common Tokens for Each Term")

    ## Selecting by n

When tokenizing the abstracts without removing stopwords, it is clear
that stop words like “the” and “of” are most abundant. The first
noticeable words that are not stop words are “covid” and “19,” which
would be expected given the context of the dataset. Upon removing stop
words, it is evident that the list of top 20 counted words changes and
is more suited to give us better details about the dataset. After
removing stop words, the top 5 common tokens were “covid,” “patients,”
“cancer,” “prostate,” and “disease.”

By search term, the top 5 words are:

-   “covid” - covid (7275), 19 (7035), patients (2293), disease (943),
    pandemic (800)

-   “prostate cancer” - cancer (3840), prostate (3832), patients (934),
    treatment (926), disease (652)

-   “preeclampsia” - pre (2038), eclampsia (2005), preeclampsia (1863),
    women (1196), pregnancy (969)

-   “cystic fibrosis” - fibrosis (867), cystic (862), cf (625), patients
    (586), disease (400)

-   “meningitis” - patients (446), meningitis (429), meningeal (219),
    csf (206), clinical (187)

Then, abstracts were tokenized into bigrams, and stop words were removed
to avoid bigrams containining them too. A visual containing columns for
each of the ten most common bigrams was created.

    gitData %>%
      unnest_ngrams(output = bigram, input = abstract, n = 2) %>%
      separate(bigram, c("word1", "word2"), sep = " ") %>% 
      filter(!word1 %in% stop_words$word) %>%
      filter(!word2 %in% stop_words$word) %>% 
      unite(bigram, word1, word2, sep = " ") %>% 
      count(bigram, sort = TRUE) %>%
      top_n(10) %>%
      ggplot(aes(x = n, y = fct_reorder(bigram,n))) +
      geom_col(color = "#8A2893", fill = "#C470CB") +
      labs(x = "Occurrences", y = "Bigram", title = "Top 10 Common Bigrams")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-bigrams-1.png)

Lastly, for each word-search term combination, TF-IDF values were
calculated.

    # 
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, term) %>%
      bind_tf_idf(word, term, n) %>%
      arrange(desc(tf_idf)) %>% 
      group_by(term) %>%
      top_n(5,tf_idf) %>%
      arrange(desc(term)) %>%
      knitr::kable(caption = "Top 5 Words for Each Search Term by TF-IDF Values, sorted by Term")

Based on the results in the produced table, there are certainly some
different results than the table without considering TF-IDF. For each
term, there were new words like:

-   “prostate cancer” - androgen, psa, prostatectomy, castration

-   “preeclampsia” - maternal, gestational

-   “meningitis” - pachymeningitis, meninges

-   “cystic fibrosis” - cftr, sweat

-   “covid” - sars, coronavirus, cov
