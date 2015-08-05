# Test Map of PostGIS data
*powered by RESTify, PostGIS, and Leaflet maps*

A basic instant mapping demo using PostGIS, node-restify, LeafLet Maps and map tiles from Stamen, to visualize data from a postgres database. Based on Ryan Jarvinien's restify-postgis repo (https://github.com/ryanj/restify-postGIS) and the related post on OpenShift.com: [https://www.openshift.com/blogs/instant-mapping-applications-with-postgis-and-nodejs](https://www.openshift.com/blogs/instant-mapping-applications-with-postgis-and-nodejs)


### Basic Configuration
This app uses the `config` npm module, which loads it's configuration details from the `config/defaults.json` file.  This configuration takes advantage of several environment variables whenever they are available.  On OpenShift, many of these values are automatically provided for your application by their associated cartridge add-on service:

    module.exports = {
      port: process.env.PORT || process.env.OPENSHIFT_NODEJS_PORT || 3000,
      ip: process.env.OPENSHIFT_NODEJS_IP || '127.0.0.1',
      pg_config: process.env.OPENSHIFT_POSTGRESQL_DB_URL || 'postgresql://127.0.0.1:5432',
      table_name: process.env.OPENSHIFT_APP_NAME || 'parks'
    }

Sensible defaults allow us to run the same code in multiple environments.

If you plan on using the port-forwarded DB connection from the [previous step](#local-db-access) in your local development stage, then you will need to supply some additional DB authentication credentials to your application via the `OPENSHIFT_POSTGRESQL_DB_URL` environment variable.

### Environment Variables
Now, set your `OPENSHIFT_POSTGRESQL_DB_URL` environment variable, substituting your own `DB_USERNAME`, `DB_PASSWORD`, `LOCAL_DB_IP`, and `LOCAL_DB_PORT`:

    export OPENSHIFT_POSTGRESQL_DB_URL="postgres://DB_USERNAME:DB_PASSWORD@LOCAL_DB_IP:LOCAL_DB_PORT"

My application's command ended up looking like this:

    export OPENSHIFT_POSTGRESQL_DB_URL="postgres://admin32jk510:X_kgB-3LfUd3@127.0.0.1:5433"

This app also expects to use a Postgres `table_name` that matches your application's name (as defined within OpenShift).  When running this application on OpenShift, the `OPENSHIFT_APP_NAME` environment variable will be automatically populated.  If you didn't name your application "parks" (the default value for this option), then you will likely need to set an extra environment variable containing your table name in your local dev environment:

    export OPENSHIFT_APP_NAME=parks

#### Testing your connection
You can verify that your port-forwarding tunnel is active, and that your environment variables are configured correctly by using them to make a DB connection using the `psql` command-line client:

    psql $OPENSHIFT_POSTGRESQL_DB_URL/$OPENSHIFT_APP_NAME

This should provide a direct connection to your OpenShift-hosted database instance.

Here you can verify that your application's [automatic database import procedures](https://www.openshift.com/blogs/instant-mapping-applications-with-postgis-and-nodejs#bootstrapping) have successfully completed, by checking the number of records in our DB table:

    select count(*) from parks;

The result should be `547`.  As usual, if you named your application something other than `parks`, you'll need to modify above the command to match your particular configuration.

#### Starting your Local Webserver
With your dependencies installed, your port-forwarding tunnel established, and your environment variables set, firing up a local server should be as simple as typing:

    npm start

Your dev server should be available at the default address: [localhost:3000](http://localhost:3000)

## Deploying updates to OpenShift
When you're ready, you can push changes to your OpenShift-hosted application environment using the standard `git` workflow:

1. Add your changes to a changeset:

    `git add filename1 filename2`

2. Mark the changeset as a Commit:

    `git commit -m 'describe your changes here'`

3. Push the Commit to OpenShift

    `git push`

## License
This code is dedicated to the public domain to the maximum extent permitted by applicable law, pursuant to CC0 (http://creativecommons.org/publicdomain/zero/1.0/)
