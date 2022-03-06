# python-app

## Helm chart for a python app bundled with PostgreSQL for a case study.

This chart utilizes bitnami/postgresql chart as a dependency to deploy database for the app. It is set to replicate to 1 pod that works in read-only mode.

## Deployment

1. Clone the repo:

    ```bash
    git clone https://github.com/eliminyro/python-app.git
    ```
2. Build the dependencies:

    ```bash
    helm dependency build ./python-app
    ```
3. Install the 'release':

    ```bash
    helm install RELEASE-NAME -n NAMESPACE -f values.yaml ./python-app
    ```

## Parameters

| Value | Description | Required | Default value |
|-------|-------------|----------|---------------|
| image.repository | Docker image with the app itself | yes | eliminyro/pyapp |
| image.tag | Overrides the appVersion from the chart | no | "" |
| postgresql.enabled | Enables the dependency chart PostgreSQL | yes | true |
| postgresql.auth.username | Sets the username for PostgreSQL database | yes | chell |
| postgresql.auth.password | Sets the password for PostgreSQL user | yes | THECAKEISALIE |
| postgresql.auth.database | Sets the name for PostgreSQL database that app will use | yes | aperture |
| postgresql.nameOverride | Overrides the name of the entities for the PostgreSQL chart, used for their name generation | no | pgsql |
| postgresql.architecture | Switch between a standalone PostgreSQL instance and replicated cluster | no | replication |
| postgresql.primary.persistance.enabled | Enables the persistant storage for primary PostgreSQL node. Critical for storing databases | yes | enabled |
| postgresql.readReplicas.persistence.enabled | Same but for replicas | yes | enabled |
| postgresql.readReplicas.replicaCount | Number of read-only replicas for PostgreSQL instances | no | 1 |
| mail.box | Mail account for sending notifications | yes | "" |
| mail.passwd | Password for sender mail account | yes | "" |
| mail.server | Server to connect use as a sender for notifications | yes | smtp.gmail.com |
| mail.port | Server port for SMTP connection | yes | 587 |
| mail.tls | TLS switch, for turning on and off the encryption when sending notifications | yes | true |
| mail.recipient | The mailbox for recieving notifications | yes | "" |

## NOTE: It's important to specify all required parameters displayed in the table above.

The application might not work as expected otherwise.
Additionally: the persistent storage settings vary from cluster to cluster, so they should be set according to the set up the application is going to be deployed at. For additional information please check the official Kubernetes [documentation](https://kubernetes.io/docs/concepts/storage/persistent-volumes)

## Application

Application has the following functionality:

1. Accessing it via browser with http://PYAPP_ADDRESS:8080 (default servicePort is set to 8080, the app listens on 8080 as well ) will greet with the following page:

    <h3>NOTICE: No input provided.</h3>
    <p>To calculate a square of a specified integer, enter in the address bar the following:</p>
    <p style="color: blue; font-weight: bold; --darkreader-inline-color:#337dff;" data-darkreader-inline-color="">http://PYAPP_ADDRESS/?n=x</p>
    <p>where <b>PYAPP_ADDRESS</b> is IP address of the app, and <b>x</b> is the integer.</p>
    <p>No floats! 'Cause ain't no one got time for that.</p>

2. Accessing the page http://PYAPP_ADDRESS:8080/?n=x, where x is an integer, as the page suggests, will result in calculation of square of said integer. Typing in anything but integer will result in a thrown exception.

3. Accessing the page http://PYAPP_ADDRESS:8080/blacklisted will result in blocking of the visitor's IP address, notification to the recipient email about the blocking, response code 444, and the index page becoming unavailable.

4. After the 'ban', you can visit the main page and click 'Unblock me' or visit the page http://PYAPP_ADDRESS:8080/unblockme to get the IP address removed from the database and unblock yourself. I did this for testing, but left it in since it might be useful.
