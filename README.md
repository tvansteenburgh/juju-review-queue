# Review Queue


## Getting started

    $ make venv
    $ . venv/bin/activate
    $ python setup.py develop


## Setup the database

    $ venv/bin/initialize_backend_db development.ini

### redis

Celery uses redis for it's backend data store. Install redis locally and run ```redis-server```.

### Run celery worker

Run from the root project directory:

    $ celery -A reviewq.celerycfg worker -l info -B

The ```-B``` flag tells celery to run our tasks periodically:
- reviewq.tasks.import_from_lp -- Ingests from LaunchPad, every 10 minutes
- reviewq.tasks.refresh_active, -- Refresh, every 2 minutes
- reviewq.tasks.parse_tests -- Parse test results, on demand
- reviewq.tasks.refresh -- Called by refresh_active to refresh one record, on demand


### Launchpad Authentication

    $ venv/bin/initialize_lp_creds

This will open up LaunchPad in your browser, requesting OAUTH access from the Review Queue application. The credentials will be cached in ```lp-creds```

**NOTE**: This will run all actions as your user.


## Contributing

### Database Schema

The ```initialize_backend_db``` script will create ```backend.sqlite``` with the most current database schema. Modifications to the structure of an existing database are done via [alembic](https://alembic.readthedocs.org/en/latest/).

##### Migrations

1. [Create a migration script](https://alembic.readthedocs.org/en/latest/tutorial.html#create-a-migration-script):

        $ alembic revision -m "Describe your revision here"
        Generating reviewqueue/migrations/versions/1975ea83b712_describe_your_revision_here.py...done

2. Open the generated file and make your changes, to the ```upgrade()``` and ```downgrade()``` methods.

3. Run the migration:

    $ alembic upgrade head

4. Modify ```reviewq/models.py``` to reflect the schema change(s).


### TODO
- See [Issues](https://github.com/juju-solutions/review-queue/issues) on Github
- Make ```ingest_the_world``` work again -- useful for local development
- Verify the database migration steps are correct.
- Document development workflow.
