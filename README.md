[![CircleCI](https://circleci.com/gh/curationexperts/nurax.svg?style=svg)](https://circleci.com/gh/curationexperts/nurax)

# README

This is an application used for testing the state of [Hyrax](https://github.com/samvera/hyrax). The `main` branch is pinned to Hyrax
`main`, and is automatically deployed to [nurax-dev.curationexperts.com](https://nurax-dev.curationexperts.com) once per
day. The `nurax-stable` branch is pinned to the latest stable release of Hyrax,
and is deployed much less often, only when there has been a new release, to
[nurax-stable.curationexperts.com](https://nurax-stable.curationexperts.com).

## Running with Docker

See [the docker notes](DOCKER.md) to learn about running Nurax in Docker.

## Infrastructure

The nurax servers are built using the [ansible-samvera](https://github.com/curationexperts/ansible-samvera) ansible roles. These servers are maintained by Data Curation Experts as a service to [the Samvera Community](http://samvera.org).

## Auto-deploy process

There is a cron-job on `nurax-dev` running this script once per day:

```
#!/usr/local/bin/ruby

# Get the latest nurax main
# Make a branch with today's date and update hyrax
# Push and deploy that branch
# Delete the branch

today = Time.now.strftime('%Y-%m-%e-%H-%M')
`cd /home/ubuntu/nurax; git checkout main; git pull; git checkout -b "#{today}"`
`cd /home/ubuntu/nurax; bundle update hyrax`
`cd /home/ubuntu/nurax; git commit -a -m 'Daily update for "#{today}"'; git push --set-upstream origin #{today}`
`cd /home/ubuntu/nurax; BRANCH_NAME="#{today}" cap nurax-dev deploy`
`cd /home/ubuntu/nurax; git checkout main; git push origin --delete "#{today}"`
`cd /home/ubuntu/nurax; git checkout main; git branch -D "#{today}"`
```

## Questions

Please direct questions about this code or the servers where it runs to the `#nurax` channel on Samvera slack.
