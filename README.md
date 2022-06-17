# github-actions-learnings
This repository is to showcase some learnings about GitHub actions

Our GitHub [workflows](https://docs.github.com/en/actions/using-workflows/about-workflows) 
consist of [jobs](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow) that include steps. 
We use [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) for most of our jobs, 
to achieve a modular structure. While steps within a job run sequentially, jobs within a workflow are parallel by default. 
Our jobs depend on each other, so we also made use of GitHub Actions's features to control job execution 
to decide how exactly and which jobs are run for a given workflow. Among others, we defined [dependencies between jobs]
(https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow#defining-prerequisite-jobs) 
and [conditions](https://docs.github.com/en/actions/using-jobs/using-conditions-to-control-job-execution) under which a certain job should be executed. 
Often, these conditions include [status checks](https://docs.github.com/en/actions/learn-github-actions/expressions#status-check-functions), 
referring to the status of the previous job(s). 


A job can depend on other jobs with their own dependencies. In this case, the job will also depend on these implicit dependencies, but there are some particularities about that, two of which are the topic of this blogpost.


## Status of implicit dependencies affects job flow
If a job in a workflow has a dependency whose exit status is not "success", it will be skipped by default. 
For a job with implicit dependencies, this is true even for these implicit dependencies, 
meaning that it can be skipped even if all explicitly declared dependencies finished successfully. 
This behavior can be overwritten using the "always()" condition, forcing the job to be executed no matter what. 
If some conditions are in fact needed, they can be chained after the "always()" condition.

Here is a showcase for this behavior. If you'd like to see that this is indeed what happens, 
you can take a look here: https://github.com/veronikasiska/github-actions-learnings/runs/6936865645

## Data from implicit dependencies only available in "if" block
A related issue is the visibility of data from implicit dependencies. In GitHub actions, information can be accessed via contexts. 
If a job has dependencies, they get access to the outputs and exit status of all jobs they depend on via the needs context. 
For implicit dependencies, this only applies for the "if" block, for other cases (e.g. "run" block or as inputs to reusable workflows) 
they need to be declared as explicit dependencies. 

Here is a showcase for this behavior. If you'd like to see that this is indeed what happens, 
you can take a look here: https://github.com/veronikasiska/github-actions-learnings/runs/6936865645
