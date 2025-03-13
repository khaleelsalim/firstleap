<div align="center" style="font-size: 3em; font-weight: bold; color: #33;">

# *Project Scrapy-Selenium*

</div>


** change in readme **

# Contents

<!-- TOC -->
  * [Scope of the project](#scope-of-the-project)  
  * [Prerequistes](#prerequistes)
  * [Setting up the Project](#setting-up-the-project)
    * [Step 1: Open the Terminal](#step-1-open-the-terminal)
    * [Step 2: Create a Working Directory](#step-2-create-a-working-directory)
    * [Step 3: Initialize Poetry](#step-3-initialize-poetry)
    * [Step 4: Install Dependencies](#step-4-install-dependencies)
    * [Step 5: Configure Poetry Environment](#step-5-configure-poetry-environment)
    * [Step 6: Install Missing Dependencies](#step-6-install-missing-dependencies)
    * [Step 7: Download ChromeDriver](#step-7-download-chromedriver)
    * [Step 8: Configure middlewares.py](#step-8-configure-middlewarespy-)
    * [Step 9: Create Scrapy Project](#step-9-create-scrapy-project)
    * [Step 10: Integrate Selenium with Scrapy](#step-9-integrate-selenium-with-scrapy)
    * [Step 11: Generate Spider Template](#step-11-generate-spider-template)
    * [Step 12: Modify Spider Code](#step-12-Modify-spider-code)
    

<!-- TOC -->

# Scope of the project
This document outlines the steps to set up a web scraping project using Selenium and Scrapy. It includes setting up a new project environment, installing necessary dependencies, configuring a Scrapy project, and integrating Selenium for advanced scraping capabilities.

# Prerequistes
- Python installed on your system
- An IDE (Integrated Development Environment) like PyCharm.

# Setting up the Project

## Step 1: Open the Terminal
Open the terminal in your command prompt or in the IDE. If using PyCharm, navigate to the bottom left of the IDE and click on 'Terminal'.

## Step 2: Create a Working Directory
Run the following commands
* $ pwd          # Prints the current directory
* $ mkdir Project # Creates a directory named Project
* $ cd Project # Changes to the Project directory

## Step 3: Initialize Poetry
Run the following command to initialize a new Poetry project:

* $ poetry init

Specify the following dependencies when prompted:

- scrapy
- pandas
- selenium
- scrapy-selenium
- webdriver-manager

Check the created pyproject.toml file to see listed dependencies.


### Step 4: Install Dependencies

The next command installs all dependencies listed in pyproject.toml and creates a poetry.lock file.

* $ poetry install --no-root

you can see the screenshot of the pyproject.toml 

![one.png](/image/one.png)


### Step 5: Configure Poetry Environment
Ensure you're running in a Poetry environment:

* $ poetry env info

Follow the highlighted path to set up the Poetry environment in your IDE. Copy the path.

![two.png](/image/two.png)

Go to Add new interpreter -> Add Local Interpreter -> Environments -> Existing environment and paste the copied path. See the screenshots bewlow to see the steps. 

![venv1.png](/image/venv1.png)
![venv2.png](/image/venv2.png)
![venv3.png](/image/venv3.png)

### Step 6: Install Missing Dependencies
If any dependencies were missed during poetry init, install them using pip:

* $ poetry run pip install < dependency >

### Step 7: Download ChromeDriver
Download the ChromeDriver that matches your installed Chrome browser.
An image of the page is given below

[](https://googlechromelabs.github.io/chrome-for-testing/)

We have to use a headless browser engine. We can download the ChromeDriver that matches the installed Chrome browser on our operating system. Once you are here you can chose the ChromeDriver compatible with the Chrome browser. To check your current Chrome version, go to a google page settings and check the version. Check the first three digits and pick the appropriate link from the table. Here, it starts with 126. We can pick the right version of ChromeDriver for your browser.

![three.png](/image/three.png)

Copy the first link in the list, Chrome, which is the one for linux64.Paste it on a browser and that will download ChromeDriver.

### Step 8: Configure middlewares.py 
Locate middlewares.py in the scrapy-selenium sub-folder in the site-packages folder:

* $ pip show scrapy-selenium

Drag and open the file in the IDE and update it with the necessary changes from:
![mwares.png](/image/mwares.png) 
under middlewares.py.


### Step 9: Create Scrapy Project

* $ scrapy startproject CVJobs CVLibraryJobs
* $ cd CVLibraryJobs
* $ tree 

![four.png](/image/four.png)

If we get the same result as above, our Scrapy setup has been successful. 

## Step 10: Integrate Selenium with Scrapy

Add the downloaded ChromeDriver to the scraping-scraper directory and update settings.py with what you see in the screen shot.

![five.png](/image/five.png)


After that, while scrolling up you can see a text which says : 

![false.png](/image/false.png)

Change True to False. This will ignore the robots.txt rules. 

### Step 11: Generate Spider Template

Give these in the command line: 

* $ cd CVLibraryJobs
* $ scrapy genspider cv_spider www.cv-library.co.uk

This creates cv_spider.py in the spiders directory.

![eight.png](/image/eight.png)

### Step 12: Modify Spider Code
Update 'cv_spider.py' with the code given: 

![six.png](/image/six.png)

Let's break down the above code changes:

- Add start_requests function to request the review page using SeleniumRequest.
- Iterate over all the reviews on the HTML and parse them using CSS selectors.

Let's execute this spider and save the scraping results.Give this in the command line:
* $ scrapy crawl cv_spider –output cv_spider.json

Now a JSON files will be created in the project folder. The above Scrapy command will execute the spider and save the scraping results into the cv_spider.json file:

### Step 13: Running the Scraper with Iterator

The Iterator script (iterator.py) is responsible for executing the Scrapy spider (cv_spider.py). Instead of running the spider manually, the iterator automates the process using a predefined list of job-related keywords.

How the Iterator Works
 1. Reads keywords from a file (keywords.txt).
 2. Runs the Scrapy spider for each keyword using:
    $ scrapy crawl -L WARNING x_spider -o {doing_folder}/{filename}.json -a keyword={keyword}
 3. Scraped job listings and details are saved in JSON files inside the /app/data/doing folder.
 4. Once the scraping for a keyword is complete, the file is moved to the pipeline folder.

### Step 14: Data Processing Pipeline

Once scraping is complete, the JSON files move into the pipeline stage, where they are cleaned, merged, and stored in a structured database.

# Pipeline Workflow (main.py)
1. Separating Data:
   - Each scraped JSON file contains three types of records:
      - Job Listings (job-listing-page)
      - Job Details (job-details-page)
      - Company Details (com-details-page)
   - These are extracted and stored in separate Pandas DataFrames.

2. Merging & Removing Duplicates:

   - job-listing-page and job-details-page are merged based on the jobcard_id.
   - Duplicate jobcard_id entries are dropped to avoid redundancy.
   
3. Final Processing:

   - The cleaned data is inserted into a PostgreSQL database.
   - Processed files are moved from pipeline → done.

### Step 15: Database Storage
  
We store processed job data into PostgreSQL to allow structured querying.

### Step 16: Dockering the Project

Docker allows us to package our application along with its dependencies, ensuring that it runs consistently on any machine. Some key benefits:

  - Portability: Run the application on any system without installation issues.
  - Dependency Management: All required libraries are bundled in a container.
  - Scalability: Easily deploy multiple instances of the scraper.
  - Isolation: The application runs in a self-contained environment without     
    affecting the host system.

Essential Docker Files

  1. Dockerfile:
     
  The Dockerfile is a script that contains all the instructions to build a     
  Docker image for our project.
  3. docker-compose.yaml
  _docker-compose.yaml_ is a configuration file that helps us run multiple    
  containers (such as PostgreSQL and our Scrapy-Selenium pipeline) together.
  Here's a breakdown of what each service does:
     - PostgreSQL (postgres)
       - Uses the latest PostgreSQL image to store scraped job data
       - Mounts a volume (postgres_data) for database storage
       - Exposes port 5432 for database access
     - pgAdmin (pgadmin)
       - A web-based PostgreSQL administration tool for managing the database.
       - Runs on port 80, allowing easy database access.
       - Depends on the PostgreSQL service.
     - Scraper
       - Runs the Scrapy-Selenium scraper inside a Docker container
       - Mounts the data directory to share scraped data between services.
       - Depends on Chromedriver to ensure the browser is available.
     - Pipeline
       - Provides visual dashboards for monitoring database metrics.
       - Uses port 3000 for the web interface.
       - Stores data in a persistent volume (grafana_data) for retention.
     - Volumes
       - postgres_data: Stores PostgreSQL data persistently.
       - grafana_data: Stores Grafana dashboard configurations persistently.
     
       



  





Iterator script is used to run the scraping (cv_spider.py) script. When iterator runs the commmand "scrapy crawl -L WARNING x_spider -o {doing_folder}/{filename}.json -a keyword={keyword}" is executed. A new folder 'data' will be created to to handle the files.The folder 'data' has three sub folders "doing" , "pipeline" and "done". JSON files are created in the current directory. Scraped data will be saved in those JSON files. Once scraping for a keyword is over, the JSON file is moved to the pipeline folder in the same directory. 

A pipeline script is created to process the data scraped. Each JSON file has three parsing. In the pipeline scrpit separating and merging the tables happen and then the du[plicates are removed in this process and the final output JSON f 




