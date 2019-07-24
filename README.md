# Shell Command Jobs Processor

shell command job processor

## Description

The job processor parses a text file containing a list of jobs. Each job has a program of executable bash commands, and an optional list of parent jobs (dependencies) that need to be executed beforehand. The jobs are processed to determine dependencies and executed accordingly. When possible, jobs are executed concurrently. 

The program can be run standalone to process a single file or can optionally run as a Node/Express server to upload files and check job status via an API.

## Getting Started

### Dependencies

* npm 6.9.0+
* node v10.16.0+

### Installing

* `git clone https://github.com/DonaldPeat/bash-jobprocessor.git`
* `cd bash-jobprocessor`
* `npm install`

### Jobs File

* the jobs file is expected to be in the home directory of the project `path/to/bash-jobprocessor/<file>`
* each job should follow the following format:
 `job_id` - name of the job
 `program` - the shell command to be executed when the job is run
 `parent_job_ids` - optional space delimeted list of any other jobs that should be completed before executing
* jobs do not need to be listed in topological or dependent order, this is determined during processing
* example:
 ```
 # id identifying job in rest of file.
 job_id job1
 # program and arguments. run the program through the bash shell.
 program cat /tmp/file2 /tmp/file3 /tmp/file4
 # jobs depended on.
 parent_job_ids job2 job3 job4
 job_id job2
 program echo hi > /tmp/file2
 job_id job3
 program echo bye > /tmp/file3
 job_id job4
 program cat /tmp/file2 > /tmp/file4; echo again >> /tmp/file4
 parent_job_ids job2
 ```
* comments & empty lines are ignored, program commands are expected not to have newlines

### Process a Single File

 `npm run process <file_name> <thread_limit>` 

* where `file_name` is the local job file and `thread_limit` is an *optional* number indicating an upper limit for how many  concurrent jobs should be allowed to execute at any given time, if `thread_limit` is not provided, it is set to 999 by default

### Running the Server & API

 `npm run server <thread_limit>`

* where `thread_limit` is the same *optional* argument discussed above

* this will start the server viewed locally at http://localhost:3000

* there are basic html inputs to optionally upload a job file and/or check on a job's status

* select a jobs file from anywhere on your local machine and click 'Upload' 

* it will download the file into the project home directory and automatically process it 

* on successful upload it will return the first `job_id` listed in the file, any `stdout` will be displayed in terminal where server was started

* you can enter the job Id into the input and click 'Check status' to return the status of the job 

* if it is currently running it will display elipses '...' until it has completed then the status will automatically update to 'success' 

* you can optionally use the endpoint directly to check status using http://localhost:3000/<job_id> 

* if you prefer you can use node directly:

 `node index.js process <file_name> <thread_limit>` 

 `node index.js server <thread_limit>`


## Notes

A previous version used a topological sort to arange the jobs in order of dependency as in a directed graph before iterating over the jobs but the execution time difference was negligible for smaller files and my small sample testing showed the topological sort to average a little slower. 

## External modules used

express - https://www.npmjs.com/package/express
express-fileupload - https://www.npmjs.com/package/express-fileupload
