- [x] Scrape LinkedIn jobs
- [x] Define core features of the dataset
- [ ] Classify data based on the defined features
	- [ ] Core responsibilities classification
- [ ] Extract core skills required for the successful application
# Log
## Scrape LinkedIn
Done with `linkedin-jobs-scraper` Python package.
💡 Jobs in Korean lang could be translated with [googletrans](https://pypi.org/project/googletrans/)
## Core features of the dataset

| Feature            | Description                                             |
| ------------------ | ------------------------------------------------------- |
| Tech stack         | Array of the required technologies                      |
| Responsibilities   | List of the responsibilities directly from the employer |
| Position           | One of Lead, Senior, Middle, Other                      |
| Experience         | Required amount of years                                |
| Degree requirement | Required degree                                         |
| Salary             | Year salary in USD                                      |
💡Tech stack size for the given position & years of experience
❓Other positions
## Data classification
### Responsibilities classification
💡Try to use `ChatGPT` to decide if the given responsibilities will be OK for me by describing to it my experience, wishes and red flags.
❓Does binary classification is right here?
❓How to make fancier classification?

[Several ideas about classification with LLMs](https://community.openai.com/t/classification-with-generative-models/600277)

## Required skills & keywords extraction