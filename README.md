<div align="center" style="font-size: 3em; font-weight: bold; color: #33;">

# *Project Scrapy-Selenium*

</div>

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
* $ pwd          # Prints the current d irectory
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

![one.png](/home/khaleel/readme/one.png)


### Step 5: Configure Poetry Environment
Ensure you're running in a Poetry environment:

* $ poetry env info

Follow the highlighted path to set up the Poetry environment in your IDE. Copy the path.

![two.png](/home/khaleel/readme/two.png)

Go to Add new interpreter -> Add Local Interpreter -> Environments -> Existing environment and paste the copied path.

### Step 6: Install Missing Dependencies
If any dependencies were missed during poetry init, install them using pip:

* $ poetry run pip install < dependency >

### Step 7: Download ChromeDriver
Download the ChromeDriver that matches your installed Chrome browser version from the 
[](https://googlechromelabs.github.io/chrome-for-testing/)

We have to use a headless browser engine. We can download the ChromeDriver that matches the installed Chrome browser on our operating system. Once you are here you can chose the ChromeDriver compatible with the Chrome browser. To check your current Chrome version, go to a google page settings and check the version. Check the first three digits and pick the appropriate link from the table. Here, it starts with 126. and we can pick the right version of ChromeDriver.

![three.png](/home/khaleel/readme/three.png)

Copy the first link in the list, Chrome, which is the one for linux64.

### Step 8: Configure middlewares.py 
Locate middlewares.py in the scrapy-selenium sub-folder in the site-packages folder:

* $ pip show scrapy-selenium

Drag and open the file in the IDE and update it with the necessary changes from:
![middlewares.png](/home/khaleel/Pictures/Screenshots/middlewares.png) 
under middlewares.py.



### Step 9: Create Scrapy Project

* $ scrapy startproject CVJobs CVLibraryJobs
* $ cd CVLibraryJobs
* $ tree 

![four.png](/home/khaleel/readme/four.png)

If we get the same result as above, our Scrapy setup has been successful. 

## Step 10: Integrate Selenium with Scrapy

Add the downloaded ChromeDriver to the scraping-scraper directory and update settings.py with what you see in the screen shot.

![five.png](/home/khaleel/readme/five.png)


After that, while scrolling up you can see a text which says : 

![false.png](/home/khaleel/Pictures/Screenshots/false.png)

Change True to False. This change will ignore the robots.txt rules. 

### Step 11: Generate Spider Template

Give these in the command line: 

* $ cd CVLibraryJobs
* $ scrapy genspider cv_spider www.cv-library.co.uk

This creates cv_spider.py in the spiders directory.

### Step 12: Modify Spider Code
Update 'cv_spider.py' with the code given: 

![six.png](/home/khaleel/readme/six.png)

Let's break down the above code changes:

- Add start_requests function to request the review page using SeleniumRequest.
- Iterate over all the reviews on the HTML and parse them using CSS selectors.

Let's execute this spider and save the scraping results.Give this in the command line:
* $ scrapy crawl cv_spider –output cv_spider.json

Now a json file will be created in the project folder. The above Scrapy command will execute the spider and save the scraping results into the cv_spider.json file:





