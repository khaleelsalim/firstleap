# My little project

SCRAPY PRO

<!-- TOC -->
* [Scrapy Project](#scrapy-project)
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
  * [Step 13: Running the Scraper with Iterator](#step-13-Running-the-Scraper-with-Iterator)
  * [Step 14: Data Processing Pipeline (main.py)](#step-14-data-processing-pipeline-mainpy)
  * [Step 15: Database Structure](#step-15-database-structure)
    * [Tables Created in PostgreSQL](#tables-created-in-postgresql)
  * [Step 16: Docker and Containerization](#step-16-docker-and-containerization)
    * 
    *
        
  
  
# Scope
 This project is an automated job scraping pipeline that extracts job listings, job details, and company information from CV-Library using Scrapy and Selenium. The scraped data is processed and stored in a PostgreSQL database. Docker is used for environment management, ensuring smooth execution across different systems.

# Prerequisites
- OS: Linux
- Python: Version 3.8
- Docker: Installed and running
- PostgreSQL: Version 14+

# Setting up the Project
### Step 1: Open the Terminal or IDE 

- Open the terminal in your command prompt or in the IDE. If using PyCharm, navigate to the bottom left of the IDE and click on 'Terminal'.

### Step 2: Create a Working Directory

Run the following commands

- pwd # Prints the current directory
- mkdir Project # Creates a directory named Project
- cd Project # Changes to the Project directory

### step 3: Initialize Poetry

Run the following command to initialize a new Poetry project:

- poetry init

Specify the following dependencies when prompted:

- scrapy 
- pandas
- selenium
- scrapy-selenium
- webdriver-manager

Check the created **pyproject.toml** file to see listed dependencies.

### Step 4: Install Dependencies

The next command installs all dependencies listed in pyproject.toml and creates a poetry.lock file.

- poetry install --no-root

### Step 5: Configure Poetry Environment

Ensure you're running in a Poetry environment:

- poetry env info

Follow the highlighted path to set up the Poetry environment in your IDE. Copy the path.

![venv1](/home/khaleel/Projects/first_project/image/venv_info.png)

Go to Add new interpreter -> Add Local Interpreter -> Environments -> Existing environment and paste the copied path. See the screenshots bewlow to see the steps.

![venv1](/home/khaleel/Projects/first_project/image/venv1.png)

![venv2](/home/khaleel/Projects/first_project/image/venv2.png)

![venv3](/home/khaleel/Projects/first_project/image/venv3.png)

### Step 6: Install Missing Dependencies

If any dependencies were missed during poetry init, install them using pip:

- poetry run pip install < dependency >

### Step 7: Download ChromeDriver

Download the ChromeDriver that matches your installed Chrome browser. An image of the page is given below


We have to use a headless browser engine. We can download the ChromeDriver that matches the installed Chrome browser on our operating system. Once you are here you can chose the ChromeDriver compatible with the Chrome browser. To check your current Chrome version, go to a google page settings and check the version. Check the first three digits and pick the appropriate link from the table. Here, it starts with 126. We can pick the right version of ChromeDriver for your browser.

![Chrome_driver](/home/khaleel/Projects/first_project/image/three.png)

Copy the first link in the list, Chrome, which is the one for linux64.Paste it on a browser and that will download ChromeDriver.

### Step 8: Configure middlewares.py

Locate middlewares.py in the scrapy-selenium sub-folder in the site-packages folder:

- pip show scrapy-selenium

![middlewares](/home/khaleel/Projects/first_project/image/mwares.png)
under middleware.py

### Step 9: Create Scrapy Project

- scrapy startproject CVJobs CVLibraryJobs
- cd CVLibraryJobs
- tree

![directory](/home/khaleel/Projects/first_project/image/directoryshow.png)

If we get the same result as above, our Scrapy setup has been successful.

### Step 10: Integrate Selenium with Scrapy

Add the downloaded ChromeDriver to the scraping-scraper directory and update settings.py with what you see in the screen shot.

![selenium](/home/khaleel/Projects/first_project/image/seleniumdriver800.png)

After that, while scrolling up you can see a text which says :

![robot_text](/home/khaleel/Projects/first_project/image/robotext.png)

Change True to False. This will ignore the robots.txt rules.

### Step 11: Generate Spider Template

Give these in the command line:

- cd CVLibraryJobs
- scrapy genspider cv_spider www.cv-library.co.uk

This creates cv_spider.py in the spiders directory.

![code1](/home/khaleel/Projects/first_project/image/eight.png)

### Step 12: Modify Spider Code

Update 'cv_spider.py' with the code given:

![](/home/khaleel/Projects/first_project/image/six.png)

Let's break down the above code changes:

- Add start_requests function to request the review page using SeleniumRequest.
- Iterate over all the reviews on the HTML and parse them using CSS selectors.

Let's execute this spider and save the scraping results.Give this in the command line:

- scrapy crawl cv_spider –output cv_spider.json

Now a JSON file will be created in the project folder. The above Scrapy command will execute the spider and save the scraping results into the cv_spider.json file:
In this situation we have an iterator script which is coming up next 

### Step 13: Running the Scraper with Iterator

The Iterator script (iterator.py) is responsible for executing the Scrapy spider (cv_spider.py). Instead of running the spider manually, the iterator automates the process using a predefined list of job-related keywords.

How the Iterator Works

1. Reads keywords from a file (keywords.txt).
2. Runs the Scrapy spider for each keyword using: $ scrapy crawl -L WARNING x_spider -o {doing_folder}/{filename}.json -a keyword={keyword}
3. Scraped job listings and details are saved in JSON files inside the /app/data/doing folder.
4. Once the scraping for a keyword is complete, the file is moved to the pipeline folder.

### Step 14: Data Processing Pipeline (main.py) 

Once the data is scraped and stored in JSON format, the next step is processing and inserting it into a PostgreSQL database. The main.py script is responsible for:

    - Reading the JSON files from the pipeline folder.
    - Extracting job listing data, job details, and company details separately.
    - Cleaning the data, removing duplicates, and ensuring consistency.
    - Inserting the processed data into the PostgreSQL database.
    - Moving the processed JSON files to the done folder.

Pipeline Flow

    - main.py continuously monitors the pipeline folder for new JSON files.
    - If a new file is found, it is read into a Pandas DataFrame.
    = The script extracts and processes job listings, job details, and company details.
    - The cleaned data is inserted into the job_listing, job_details, and company_details tables in PostgreSQL.
    - After successful insertion, the JSON file is moved to the done folder.

### Step 15: Database Structure
To efficiently store and query the scraped data, PostgreSQL is used. The main.py script creates tables (if they do not exist) and inserts data into them.

# Tables Created in PostgreSQL

The following tables are created:

1. job_listing

    jobcard_id (Primary Key)
    jobcard_page_link
    job_title
    company_name
    location
    job_type
    salary
    posted_date
    scraping_date
    search_phrase

2. job_details

    jobcard_id (Primary Key)
    jobcard_page_link
    job_title
    company_name
    job_reference
    start_date
    job_description
    scraping_date

3. company_details
     
    company_id (Primary Key)
    company_name
    company_page_link
    live_jobs_count
    company_address
    website
    company_description
    scraping_date

### Step 16: Docker and Containerization

To ensure that the project runs efficiently and is easy to deploy on different systems, Docker is used to containerize the application.
Docker allows packaging the application along with its dependencies into a container. This makes deployment easier and ensures that the application runs consistently across different environments.

#### Required Docker Files

The following Docker files are required:

1. Dockerfile
    - Defines how the application should be built inside the container.
    - Installs Python, dependencies, and required services (PostgreSQL). 
    - Copies the project files into the container and sets up the execution environment.
2. docker-compose.yaml
    - Defines multiple services (e.g., the Scrapy Spider and the PostgreSQL database).
    - Ensures that all required components are started together.

### Step 17: Setting Up the Docker Environment

1. Install Docker and Docker Compose
Make sure Docker and Docker Compose are installed on your system. If not, install them using:

##### For Ubuntu

    $ sudo apt update
    $ sudo apt install docker.io -y
    $ sudo apt install docker-compose -y

#####  Fpr Verify Installation

    $ docker --version
    $ docker-compose --version  

2. Create a Dockerfile

Inside the project directory, create a file named Dockerfile with the following content:

          FROM python:3.12-alpine
          
          RUN pip install poetry==1.8.3
          
          ENV POETRY_NO_INTERACTION=1 \
              POETRY_VIRTUALENVS_IN_PROJECT=1 \
              POETRY_VIRTUALENVS_CREATE=1 \
              POETRY_CACHE_DIR=/tmp/poetry_cache \
              PYTHONUNBUFFERED=1
          
          WORKDIR /app
          COPY app/main1.py ./main.py
          COPY app/pyproject.toml app/poetry.lock ./
          
          # Create the /app/data directory
          # RUN mkdir -p /app/data
          
          RUN apk update && apk add python3-dev \
                  gcc \
                  libc-dev
          
          RUN poetry install --without dev --no-root
    
          ENTRYPOINT ["poetry", "run", "python", "main.py"]

### Step 18: Configuring docker-compose.yml

To manage multiple services (Scrapy, PostgreSQL), we use docker-compose.

1. Create a docker-compose.yml file

Inside the project directory, create docker-compose.yml:

    services:
      postgres:
        image: postgres:latest
        container_name: postgres
        restart: always
        environment:
          POSTGRES_USER: admin
          POSTGRES_PASSWORD: admin
          POSTGRES_DB: mydatabase
        ports:
          - "5432:5432"
        volumes:
          - postgres_data:/var/lib/postgresql/data
    
      pgadmin:
        image: dpage/pgadmin4:latest
        container_name: pgadmin
        restart: always
        environment:
          PGADMIN_DEFAULT_EMAIL: admin@example.com
          PGADMIN_DEFAULT_PASSWORD: admin
        ports:
          - "80:80"
        depends_on:
          - postgres
    
    
      api:
        build:
          context: ./Services/API
          dockerfile: Dockerfile
        container_name: api
    
      scraper:
        build:
          context: ./Services/scraper
          dockerfile: Dockerfile
          no_cache: true #change if you dont need to rebuild.
        container_name: scraper
        volumes:
          - ./Services/scraper/app/data:/app/data # added
    
        depends_on:
          chromedriver:
            condition: service_healthy
    
      chromedriver:
        image: selenium/standalone-chrome
        shm_size: "2g"
        ports:
          - "4444:4444"
          - "5900:5900"
        healthcheck:
          test: [ "CMD", "curl", "-f", "http://localhost:4444/wd/hub/status" ]
          interval: 30s      # Time between health check attempts
          timeout: 10s       # Maximum time to wait for a response
          retries: 10        # Number of retries before the service is marked as unhealthy
          start_period: 15s  # Initial delay before starting health checks
    
      pipeline:
        build:
          context: ./Services/pipeline
          dockerfile: Dockerfile
        container_name: pipeline
        environment:
          DATABASE_HOST: postgres
          DATABASE_PORT: 5432
          DATABASE_USER: admin
          DATABASE_PASSWORD: admin
          DATABASE_NAME: mydatabase
        depends_on:
          - postgres
        volumes:
          - ./Services/scraper/app/data:/app/data # added
    
    
      grafana:
        image: grafana/grafana:latest
        container_name: grafana
        restart: always
        environment:
          - GF_SECURITY_ADMIN_USER=admin
          - GF_SECURITY_ADMIN_PASSWORD=admin
        ports:
          - "3000:3000"
        volumes:
          - grafana_data:/var/lib/grafana
        depends_on:
          - postgres
    
    volumes:
      postgres_data:
        driver: local
      grafana_data:
        driver: local

2. Running the Docker Containers

To run the Docker container, use the following command:
    
    $ docker compose up -d --build
    
This command starts the containers in the background.

This will: 
- Start PostgreSQL as a service.
- Build and start the Scrapy Spider inside a container.
- Automatically process scraped data and store it in the database.
- Start the Grafana as a service.
- Start the pgAdmin as a service.
- Start the Selenium Chrome driver as a service.
- Start the API as a service.
- Start the pipeline as a service.
- Start the scraper as a service.

### Step 19: Monitoring and Managing Docker Containers

Once the containers are running, you can use the following commands to manage them.

- docker compose ps
- docker compose stop
- docker compose start
- docker compose restart
- docker compose logs
- docker compose down

There are more commands available in the Docker Compose documentation.

[](https://docs.docker.com/reference/cli/docker/compose/)



















### Scraper
In the build process we need to replace middleware.py in the library of scrapy itself 
https://scrapfly.io/blog/web-scraping-dynamic-web-pages-with-scrapy-selenium/
