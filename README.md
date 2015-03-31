[![Build Status](https://travis-ci.org/Robotix/robo_web.svg?branch=master)](https://travis-ci.org/Robotix/robo_web)
[![Coverage Status](https://coveralls.io/repos/Robotix/robo_web/badge.svg)](https://coveralls.io/r/Robotix/robo_web)
#The Official repository for the next iteration of Robotix.in

##Installation Prerequisites:
Refer [requirements.txt](https://github.com/Robotix/robo_web/blob/master/requirements.txt).

##Workflow:
**Fork the repo, create a new branch, make changes and send a pull request**
-  Fork the repository and request for an issue to be assigned.  
   For the list of issues, [click here](https://github.com/Robotix/robo_web/issues)
-  Create a branch in the fork. Create a pull request once the issue is resolved.
-  Every code must be tested. Refer the [Official Django Documentation](https://docs.djangoproject.com/en/1.6/) to learn why.  
**”Code without tests is broken by design.”**

##Local Set Up:
- Run the following commands in the terminal after a clone.
  ```sh
  $ python manage.py syncdb
  $ python manage.py migrate
  ```
- After a change to the DB Schema, run the following:  
  ```sh
  $ python manage.py makemigrations <APP_NAME>
  $ python manage.py migrate <APP_NAME>
  ```

##Before committing
- Check that your code adheres to PEP8 guidelines.
  ```sh
  $ pep8 --exclude=migrations .
  ```
- Check that all tests pass.
  ```sh
  $ python manage.py test
  ```