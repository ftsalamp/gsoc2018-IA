# Google Summer of Code 2018 Final Report

This repository will be used as a place to best describe and include the work I have done for the **Internet Archive** as a student developer for the Google Summer of Code 2018. 

For details on each of my applications and information on how they work please look at the **README** files in their repositories.

## Project

My project was **Idea 5 - Integrate the “Scanner” software into the Wayback Machine**. The aim of this project was to identify changes in archives for a given URL and highlight those changes by integrating the [web-monitoring software](https://github.com/edgi-govdata-archiving/web-monitoring) into the Wayback Machine and help to further advance it. This further translates into improving the web-monitoring software, recognising which components are useful to the Internet Archive’s implementation, how they can be best used and extracting and using them in a new project. 

The web-monitoring project consists of four components:

1. [web-monitoring-db](https://github.com/edgi-govdata-archiving/web-monitoring-db) A Ruby on Rails app that serves database data via a REST API, serves diffs and collects human-entered annotations.
2. [web-monitoring-ui](https://github.com/edgi-govdata-archiving/web-monitoring-ui) A React front-end that provides useful views of the diffs. It communicates with the Rails app via JSON.
3. [web-monitoring-processing](https://github.com/edgi-govdata-archiving/web-monitoring-processing) A Python backend that ingests new captured HTML, computes diffs, performs prioritization/filtering, and populates databases for the Rails app.
4. [web-monitoring-versionista-scraper](https://github.com/edgi-govdata-archiving/web-monitoring-versionista-scraper) A set of Node.js scripts used to extract data from Versionista and load it into the database.

After carefully studying these components it was apparent that only component number 3 and part of component number 2 were required for our implementation.

## Implementation

### Web-monitoring-processing

Since we were going to use [web-monitoring-processing](https://github.com/edgi-govdata-archiving/web-monitoring-processing) as a whole, this was the best place to begin work for the GSoC. My mentor encouraged me to find issues and ways to improve this project. We were mainly going to use the **wm-diffing-server** so I made [a fork](https://github.com/ftsalamp/web-monitoring-processing/) of the GitHub repository and started digging deeper at the related code. In total, I made 3 Pull Requests (see table 1) that consisted of 12 commits (see table 2) on the project.

Table 1

Pull Request|Link
---|---
Diffing server exception handling #185 | https://github.com/edgi-govdata-archiving/web-monitoring-processing/pull/185
Add tornado debug env value #187 | https://github.com/edgi-govdata-archiving/web-monitoring-processing/pull/187 
Cors #188 | https://github.com/edgi-govdata-archiving/web-monitoring-processing/pull/188 

Table 2

Commit|Link
---|---
Returning some exceptions in JSON format | https://github.com/ftsalamp/web-monitoring-processing/commit/6de1d380d69d1f983ce80c3d8d8a925e61bddf2f 
Code cleanup, used send_error method | https://github.com/ftsalamp/web-monitoring-processing/commit/b54ad857771573ccbc9a5c757d4ed10c456cb1ae 
Handling more errors | https://github.com/ftsalamp/web-monitoring-processing/commit/65aa1c6fa66fd665b55fa80ee5b6b37cec12d717 
Diffing server unit tests, typo correction | https://github.com/ftsalamp/web-monitoring-processing/commit/60dfc39f543ebc495c5b45612ec5f92b3388292e 
Allocating a new port for each test, all tests working | https://github.com/ftsalamp/web-monitoring-processing/commit/9e38fed8ac70a3cf1c715f3baa71cefdf9545013
Implementation of requested changes | https://github.com/ftsalamp/web-monitoring-processing/commit/3f92aeec6dac18800a7075ef061087463ce44500
Implementation of requested changes 2 | https://github.com/ftsalamp/web-monitoring-processing/commit/6441547d7cddca9ce405738846371884a4046a36
Implementation of requested changes 3 | https://github.com/ftsalamp/web-monitoring-processing/commit/50ef7dbaffca96c1b86f35badbb75b5ad31e2a88
Add tornado debug env value | https://github.com/ftsalamp/web-monitoring-processing/commit/b68dc541ad0c9f61bd2977a980b7339530f1c62e
Read env at top, fix string booleam value | https://github.com/ftsalamp/web-monitoring-processing/commit/3ede735581a28b85e1ebdb310b10736abc08fa86
Change DEBUG env value name | https://github.com/ftsalamp/web-monitoring-processing/commit/2e491a13f663781ff5afb4bdaedb3c002601f0d6
Handle env values better | https://github.com/ftsalamp/web-monitoring-processing/commit/01188584ed8956ae1020b134055d014d14d6c9a6

## Web-monitoring-ui

The *web-monitoring-ui* project is a complex React front-end application that communicates with the *web-monitoring-db* Rails backend, undertakes the task of displaying pages for which snapshots exist, and displays their differences. After studying the component, I decided that only the components that render the `diff views` are what our project requires.

## Wayback-diff

[Wayback-diff](https://github.com/ftsalamp/wayback-diff) is the React component which I created for the Google Summer of Code 2018 and will be integrated into the Wayback Machine. It queries the *wm-diffing-server (part of the web-monitoring-processing app)* for the differences between two captures of a webpage. *wm-diffing-server* in turn fetches the two captures from the Wayback Machine, calculates their differences and returns a JSON response to the component. After that, *wayback-diff* renders the snapshots with their differences marked in the user’s browser. This component is exported as an ES module and can be used in any React project.

As an example project and in order to demo it’s usage, I created the [wayback-diff-test repository](https://github.com/ftsalamp/wayback-diff-test). This repository contains an empty project, as it would be initialized using the ```yarn create react-app``` command. It is merely importing wayback-diff and it is using it under a *BrowserRouter*.

Trying the same thing in a minimal project my mentor created the [minimal-react-starter](https://github.com/vbanos/minimal-react-starter/tree/experiment) repository. There, he tried to import and use my component. This is where with [a Pull request](https://github.com/vbanos/minimal-react-starter/pull/1) I resolved a configuration issue with *express* thus, providing anyone with guidance if they want to use this component with express. 

## wayback-discover-diff

[Wayback-discover-diff](https://github.com/ftsalamp/wayback-discover-diff) is a Python server I built which runs on Flask and Celery and uses Redis as a database that handles and calculates the Simhash value of snapshots of webpages.

During the development of *wayback-discover-diff*, my mentor and I saw that it’s execution was not fast enough to be practical to use in any use case. So I started looking into making improvements in the algorithm, wherever possible. When all the improvements were made, I wrote [a detailed report](https://drive.google.com/file/d/1dMe2f8xggzNf2T74vgiBjIFTaMOuZdDC/view?usp=sharing) describing them and the performance boost each of them offered. The result of my improvements brought an **94.32% improvement** of the runtime.

## Further work


### wayback-diff

Depending on the look of the Wayback Machine’s User Interface, wayback-diff might require minor changes to its look to match the WMB’s style.

Also, wayback-diff needs more work to be ready for production. We must work to make it scalable. This means to handle URLs with large numbers of captures (100k+). Last but not least, there should be better error handling (special cases like unusual file types or encodings, etc).


### wayback-discover-diff

wayback-discover-diff also needs more work to be ready for production. This works includes writing more tests, testing the app under a considerable load (100+ concurrent requests) and having better error handling.

A feature that is missing right now from the wayback-discover-diff is to calculate how much two snapshots have changed. Since their Simhash value would have already been calculated and would be in the database, getting the distance between the two snapshots’ simhash values would be a very useful piece of information. Integrating this kind of information in the Wayback Machine would help its users identify how much a webpage has changed from a specific moment in time. In addition, it would help save both resources for the Internet Archive and save users’ time as they would know which snapshots it makes sense to compare. Comparing snapshots that are 100% or 5% identical would not help the users gain any insights on how the page has changed.
