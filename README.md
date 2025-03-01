
# Wikipedia Political Figures Analysis
####  Considering Bias in Data

## Project Goal
This project analyzes potential biases in Wikipedia articles about political figures across countries and regions. Using Wikipedia article data and population statistics, the study explores disparities in both coverage and quality of articles. ORES machine learning predictions help assess the quality of articles.

The key goals of this analysis are:
1. Identify countries with the highest and lowest number of articles per capita.
2. Identify countries with the highest and lowest proportion of high-quality articles per capita.
3. Rank regions by total articles per capita and high-quality articles per capita.

## Data Sources and Licenses

### 1. Wikipedia Article Data
The data was collected from the Wikipedia [Category:Politicians by nationality](https://en.wikipedia.org/wiki/Category:Politicians_by_nationality). It includes a list of articles about politicians from various countries, with fields like article title, country, revision ID, and article quality (e.g., FA, GA, Start, Stub).

- **Dataset**: `politicians_by_country_AUG.2024.csv`
- **License**: Compliant with [Wikimedia Foundation Terms of Use](https://foundation.wikimedia.org/wiki/Policy:Terms_of_Use).

### 2. Population Data
The population data is from the [World Population Data Sheet](https://www.prb.org/international/indicator/population/table/) and includes population statistics in millions.

- **Dataset**: `population_by_country_AUG.2024.csv` 

## Intermediary Files
- **all_pageinfo_responses.json**: API response of wikipedia page information for each politician.
The JSON file contains detailed information about politicians. The structure of the JSON file is designed to store key attributes for each politician, indexed by their name.

#### JSON Structure

```json
{
    "Politician Name": {
        "pageid": 27428272,
        "ns": 0,
        "title": "Politician Name",
        "contentmodel": "wikitext",
        "pagelanguage": "en",
        "pagelanguagehtmlcode": "en",
        "pagelanguagedir": "ltr",
        "touched": "2024-09-27T01:07:35Z",
        "lastrevid": 1231655023,
        "length": 1357,
        "talkid": 27595416,
        "fullurl": "https://en.wikipedia.org/wiki/Politician_Name",
        "editurl": "https://en.wikipedia.org/w/index.php?title=Politician_Name&action=edit",
        "canonicalurl": "https://en.wikipedia.org/wiki/Politician_Name"
    }
}
```

- **ores_responses.json**: API response that give article information based on revision ID.  
The JSON file contains detailed information regarding the quality assessment of articles. The structure of the JSON file is designed to store key attributes for each revision ID, which is used as a key in the JSON structure.

#### JSON Structure

```json
{
    "1231655023": {
        "enwiki": {
            "models": {
                "articlequality": {
                    "version": "0.9.2"
                }
            },
            "scores": {
                "1231655023": {
                    "articlequality": {
                        "score": {
                            "prediction": "Stub",
                            "probability": {
                                "B": 0.007563164541978764,
                                "C": 0.010571998067933679,
                                "FA": 0.0014567448768872152,
                                "GA": 0.00350824677167893,
                                "Start": 0.04243220742433865,
                                "Stub": 0.9344676383171826
                            }
                        }
                    }
                }
            }
        }
    }
}
```

## Output Files

- **wp_politicians_by_country.csv**: Final merged dataset of Wikipedia articles and population data.
This CSV file contains the following columns:

| Column Name      | Data Type | Description                                                      |
|------------------|-----------|------------------------------------------------------------------|
| `country`        | String    | The name of the country where the politician is from.           |
| `region`         | String    | The geographical region associated with the country.            |
| `population`     | Float     | The population of the country as a numerical value.             |
| `article_title`  | String    | The title of the Wikipedia article related to the politician.    |
| `revision_id`    | String    | The revision ID of the article, which is a unique identifier.   |
| `article_quality` | String    | The assessed quality of the article (e.g., Stub, Start, GA).   |

### Example Row

| country   | region   | population | article_title           | revision_id | article_quality |
|-----------|----------|------------|-------------------------|-------------|-----------------|
| Australia | Oceania  | 26.6       | Australian Politician    | 1234567890  | Start           |
| Canada    | North America | 38.0 | Canadian Politician      | 0987654321  | Stub            |


- **wp_countries-no_match.txt**: List of countries without matching population data.
This text file contains a list of countries and their corresponding regions where there were no matches found in the dataset. Each entry is formatted as follows:  
#### Schema

| Field        | Data Type | Description                                   |
|--------------|-----------|-----------------------------------------------|
| `country`    | String    | The name of the country for which there was no match in the data. |
| `region`     | String    | The geographical region associated with the country. |

### Notes

- Each entry is printed on a separate line.
- The file contains only countries where no corresponding entry was found in the merged dataset.
- The regions are included to provide additional context about the geographical distribution of the unmatched countries.

## Processing Workflow - Usage

The jupyter notebook DataCollection_and_Analysis contains the code for collecting data using the APIs and also the processing and analysis.  
Use this notebook and run the cells sequentially to reproduce the results. 

### Step 1: Retrieve Wikipedia Page Data
The script retrieves information for articles about politicians and stores it in an intermediary CSV.

### Step 2: Fetch Article Quality from ORES
ORES API predicts the quality of articles. Successful responses are recorded, while failed requests are logged separately.  
IN this step, the code runs for 2+ hours based on the GPU capacity and the tool used to run jupyter.  

### Step 3: Merge with Population Data
The article data is merged with population statistics, and per capita metrics are calculated.

### Step 4: Data Analysis
Six key tables are generated:

1. **Top 10 Countries by Coverage**: Countries with the most articles per capita.
2. **Bottom 10 Countries by Coverage**: Countries with the fewest articles per capita.
3. **Top 10 Countries by Article Quality**: Countries with the most high-quality articles per capita.
4. **Bottom 10 Countries by Article Quality**: Countries with the fewest high-quality articles per capita.
5. **Regions by Total Article Coverage**: Regions ranked by articles per capita.
6. **Regions by High-Quality Article Coverage**: Regions ranked by high-quality articles per capita.

## Handling Special Cases
- **Zero Population Handling**: Countries with zero or missing population data are filtered from the analysis.
- **Hierarchical Region Assignment**: Countries are assigned to the closest geographic region based on hierarchy.
- **Artical details missing**: For certain rev IDs, the API did not give a response, this error rate is highlighted in the notebook.  


## Key Insights
1. **Articles Per Capita**: Shows how well-represented countries are based on population.
2. **High-Quality Articles Per Capita**: Identifies countries with better article quality.
3. **Regional Comparisons**: Provides insights into geographic disparities in coverage.

## Future Work
1. **Address Data Gaps**: Investigate countries with no matching data.
2. **Improve API Handling**: Enhance error handling for ORES API requests.
3. **Broader Scope**: Apply the same methodology to other Wikipedia categories.

## Results, Implications and Reflections
In this analysis, one surprising observation was the higher coverage of political figures in smaller countries like Montenegro, Luxembourg, and Maldives. This outcome reveals that countries with smaller populations tend to rank higher in articles per capita, indicating that the number of articles alone does not necessarily reflect broader societal engagement or importance. On the other hand, larger countries with higher populations, such as those in Africa or parts of Asia, appeared with much lower coverage. This highlights the limitation that countries with large populations may be underrepresented due to the articles-per-capita metric, even if they have substantial content in absolute terms.  

The analysis also revealed inherent biases related to language and internet access. European countries, such as Albania and Kosovo, not only ranked high in article coverage but also showed a higher proportion of high-quality articles. In contrast, countries from regions like Africa and Asia exhibited both low total coverage and fewer high-quality articles per capita. This reflects the linguistic bias toward English, as these regions are less likely to contribute to or engage with English-language platforms like Wikipedia. Additionally, limited internet infrastructure in certain regions restricts participation, impacting both the quantity and quality of articles produced from these areas.  Ignoring these major issues could lead to misinterpretation of data.

However, despite these biases, the dataset can still be valuable for specific use cases. For example, if a researcher is building a political knowledge base or a machine learning model for English-language applications, these data remain appropriate. While the representation might not be perfectly balanced across all regions, the content on Wikipedia is still comprehensive enough for many research contexts involving English-speaking audiences. For models focused on political figures or international relations, this dataset offers a practical starting point, provided the researcher acknowledges its limitations and supplements it with additional localized sources when needed. Some of the ways would be to acknowledge for the imbalance of data based on region, consider the population size with respect to country size.

## Conclusion
This analysis highlights biases in Wikipedia's coverage of political figures, offering insights into which countries and regions are well-represented and which are not. The framework helps ensure more balanced knowledge sharing on the platform.

