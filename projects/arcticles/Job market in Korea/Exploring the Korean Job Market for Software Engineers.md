# Introduction
South Korea has been catching a lot of attention lately as a tech hotspot, making it an intriguing destination for software engineers. As someone who's looking to immigrate there, I decided to do some homemade research to get a clearer picture of the job market. This isn't a big, formal study — just a personal project to help me understand what to expect.

To gather data, I turned to LinkedIn, scraping job listings using Python. For the initial data crunching, I used Nu Shell, which helped me sift through the raw information efficiently. Then, to dig deeper into the job descriptions and extract meaningful insights, I used ChatGPT for classification and feature extraction.

In this article, I’ll walk you through the steps I took to carry out my research, showing how you can use these techniques to explore job markets in other countries or even in different fields. By combining these tools and methods, you can gather and analyze data to gain valuable insights into any job market you're interested in. 
# Technologies overview 
- Python
- Nu shell
- ChatGPT

Python was used cause of the handy [linkedin_jobs_scraper](https://github.com/spinlud/py-linkedin-jobs-scraper) and [openai](https://github.com/openai/openai-python) packages.
Nu shell was used with experiment purpose in comparison with default bash stack.
ChatGPT
# Data extraction
To start some data is required. LinkedIn was the first website that came to my mind and there was ready to use Python package. I've copied example code, modified it a little and got ready to use script to get a `JSON` file with a list of job descriptions. Here it's source:

```python
import json
import logging
import os
from threading import Lock

from dotenv import load_dotenv

# linkedin_jobs_scraper loads env statically
# So dotenv should be loaded before imports
load_dotenv()

from linkedin_jobs_scraper import LinkedinScraper
from linkedin_jobs_scraper.events import EventData, Events
from linkedin_jobs_scraper.filters import ExperienceLevelFilters, TypeFilters
from linkedin_jobs_scraper.query import Query, QueryFilters, QueryOptions

CHROMEDRIVER_PATH = os.environ["CHROMEDRIVER_PATH"]

RESULT_FILE_PATH = "result.json"
KEYWORDS = ("Python", "PHP", "Java", "Rust")
LOCATIONS = ("South Korea",)
TYPE_FILTERS = (TypeFilters.FULL_TIME,)
EXPERIENCE = (ExperienceLevelFilters.MID_SENIOR,)
LIMIT = 500

logging.basicConfig(level=logging.INFO)
log = logging.getLogger(__name__)


def main():
    result_lock = Lock()
    result = []

    def on_data(data: EventData):
        with result_lock:
            result.append(data._asdict())

        log.info(
            "[JOB]",
            data.title,
            data.company,
            len(data.description),
        )

    def on_error(error):
        log.error("[ERROR]", error)

    def on_end():
        log.info("Scraping finished")

        if not result:
            return

        with open(RESULT_FILE_PATH, "w") as f:
            json.dump(result, f)

    queries = [
        Query(
            query=keyword,
            options=QueryOptions(
                limit=LIMIT,
                locations=[*LOCATIONS],
                filters=QueryFilters(
                    type=[*TYPE_FILTERS],
                    experience=[*EXPERIENCE],
                ),
            ),
        )
        for keyword in KEYWORDS
    ]

    scraper = LinkedinScraper(
        chrome_executable_path=CHROMEDRIVER_PATH,
        headless=True,
        max_workers=len(queries),
        slow_mo=0.5,
        page_load_timeout=40,
    )

    scraper.on(Events.DATA, on_data)
    scraper.on(Events.ERROR, on_error)
    scraper.on(Events.END, on_end)

    scraper.run(queries)


if __name__ == "__main__":
    main()
```

To download chrome driver I've made the following bash script:

```bash
#!/usr/bin/env bash
stable_version=$(curl 'https://googlechromelabs.github.io/chrome-for-testing/LATEST_RELEASE_STABLE')
driver_url=$(curl 'https://googlechromelabs.github.io/chrome-for-testing/known-good-versions-with-downloads.json' \
	| jq -r ".versions[] | select(.version == \"${stable_version}\") | .downloads.chromedriver[0] | select(.platform == \"linux64\") | .url")
wget "$driver_url"
driver_zip_name=$(echo "$driver_url" | awk -F'/' '{print $NF}')
unzip "$driver_zip_name"
rm "$driver_zip_name"
```

And my `.env` file looks like that:

```sh
CHROMEDRIVER_PATH="chromedriver-linux64/chromedriver"
LI_AT_COOKIE=
```

`linkedin_jobs_scraper` serializes jobs to the following DTO:

```python
class EventData(NamedTuple):
    query: str = ''
    location: str = ''
    job_id: str = ''
    job_index: int = -1  # Only for debug
    link: str = ''
    apply_link: str = ''
    title: str = ''
    company: str = ''
    company_link: str = ''
    company_img_link: str = ''
    place: str = ''
    description: str = ''
    description_html: str = ''
    date: str = ''
    insights: List[str] = []
    skills: List[str] = []
```

Example sample (description was replaced with `...` for better readability):

| query  | location    | job_id     | job_index | link                                                                         | apply_link | title                           | company   | company_link | company_img_link                                                                                                                                                                                    | place                              | description | description_html | date | insights                                                                                                                                                                                                                                                                       | skills                                                                                                                                                                             |
| ------ | ----------- | ---------- | --------- | ---------------------------------------------------------------------------- | ---------- | ------------------------------- | --------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | ----------- | ---------------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Python | South Korea | 3959499221 | 0         | https://www.linkedin.com/jobs/view/3959499221/?trk=flagship3_search_srp_jobs |            | Senior Python Software Engineer | Canonical |              | https://media.licdn.com/dms/image/v2/C560BAQEbIYAkAURcYw/company-logo_100_100/company-logo_100_100/0/1650566107463/canonical_logo?e=1734566400&v=beta&t=emb8cxAFwBnOGwJ8nTftd8ODTFDkC_5SQNz-Jcd8zRU | Seoul, Seoul, South Korea (Remote) | ...         | ...              |      | [Remote Full-time Mid-Senior level, Skills: Python (Programming Language), Computer Science, +8 more, See how you compare to 18 applicants. Try Premium for RSD0, , Am I a good fit for this job?, How can I best position myself for this job?, Tell me more about Canonical] | [Back-End Web Development, Computer Science, Engineering Documentation, Kubernetes, Linux, MLOps, OpenStack, Python (Programming Language), Technical Documentation, Web Services] |

Was generated with the following nu shell command:

```sh
# Replaces description of a job with elipsis
def hide-description [] {
    update description { |row| '...' } 
    | update description_html { |row| '...' } 
}

cat result.json 
| from  json 
| first 
| hide-description
| to md --pretty 
```

# Last steps before analysis
We already have several ready to use features (`title` and `skills`), but I want more:
- Years of experience
- Degree 
- Tech stack
- Position
- Responsibilities

So let's add them with help of ChatGPT!

```python
import json
import logging
import os

from dotenv import load_dotenv
from linkedin_jobs_scraper.events import EventData
from openai import OpenAI
from tqdm import tqdm

load_dotenv()

client = OpenAI(
    api_key=os.environ["OPENAI_API_KEY"],
)

with open("result.json", "rb") as f:
    jobs = json.load(f)

parsed_descriptions = []

for job in tqdm(jobs):
    job = EventData(**job)
    chat_completion = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {
                "role": "user",
                "content": """
                    Process given IT job description. 
                    Output only raw JSON with the following fields:
                        - Experience (amount of years or null)
                        - Degree requirement (str if found else null)
                        - Tech stack (array of strings)
                        - Position (middle, senior, lead, manager, other (describe it))
                        - Core responsibilites (array of strings)

                    Output will be passed directrly to the
                    Python's `json.loads` function. So DO NOT APPLY MARKDOWN FORMATTING
                    Example:
                    ```
                    {
                        "experience": 5, 
                        "degree": "bachelor", 
                        "stack": ["Python", "FastAPI", "Docker"], 
                        "position": "middle",
                        "responsibilities": ["Deliver features", "break production"]
                    }
                    ```

                    Here is a job description:
                """
                + "\n\n"
                + job.description_html,
            }
        ],
    )

    content = chat_completion.choices[0].message.content
    try:
        if not content:
            print("Empty result from ChatGPT")
            continue
        result = json.loads(content)
    except json.decoder.JSONDecodeError as e:
        logging.error(e, chat_completion)
        continue

    result["job_id"] = job.job_id
    parsed_descriptions.append(result)

with open("job_descriptions_analysis.json", "w") as f:
    json.dump(parsed_descriptions, f)
```

Do not forget to add `OPENAI_API_KEY` to the `.env` file

Now we can merge by `job_id` results with data from LinkedIn:

```sh
cat job_descriptions_analysis.json 
| from json 
| merge (cat result.json | from json)
| to json
| save full.json
```

Our data is ready to analyze!

```sh
cat full.json | from json | columns
╭────┬──────────────────╮
│  0 │ experience       │
│  1 │ degree           │
│  2 │ stack            │
│  3 │ salary           │
│  4 │ position         │
│  5 │ responsibilities │
│  6 │ job_id           │
│  7 │ query            │
│  8 │ location         │
│  9 │ job_index        │
│ 10 │ link             │
│ 11 │ apply_link       │
│ 12 │ title            │
│ 13 │ company          │
│ 14 │ company_link     │
│ 15 │ company_img_link │
│ 16 │ place            │
│ 17 │ description      │
│ 18 │ description_html │
│ 19 │ date             │
│ 20 │ insights         │
│ 21 │ skills           │
╰────┴──────────────────╯
```

# Analysis

```sh
let df = cat full.json | from json
```


```sh
$df
| get 'stack' 
| flatten 
| uniq --count 
| sort-by count --reverse 
| first 20 
| to md --pretty
```

| value      | count |
| ---------- | ----- |
| Python     | 185   |
| Java       | 70    |
| AWS        | 65    |
| Kubernetes | 61    |
| SQL        | 54    |
| C++        | 46    |
| Docker     | 42    |
| Linux      | 41    |
| React      | 37    |
| Kotlin     | 34    |
| JavaScript | 30    |
| C          | 30    |
| Kafka      | 28    |
| TypeScript | 26    |
| GCP        | 25    |
| Azure      | 24    |
| Tableau    | 22    |
| Hadoop     | 21    |
| Spark      | 21    |
| R          | 20    |