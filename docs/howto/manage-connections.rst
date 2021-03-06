..  Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

..    http://www.apache.org/licenses/LICENSE-2.0

..  Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.

Managing Connections
====================

Airflow needs to know how to connect to your environment. Information
such as hostname, port, login and passwords to other systems and services is
handled in the ``Admin->Connections`` section of the UI. The pipeline code you
will author will reference the 'conn_id' of the Connection objects.

.. image:: ../img/connections.png

Connections can be created and managed using either the UI or environment
variables.

See the :ref:`Connections Concepts <concepts-connections>` documentation for
more information.

Creating a Connection with the UI
---------------------------------

Open the ``Admin->Connections`` section of the UI. Click the ``Create`` link
to create a new connection.

.. image:: ../img/connection_create.png

1. Fill in the ``Conn Id`` field with the desired connection ID. It is
   recommended that you use lower-case characters and separate words with
   underscores.
2. Choose the connection type with the ``Conn Type`` field.
3. Fill in the remaining fields. See
   :ref:`manage-connections-connection-types` for a description of the fields
   belonging to the different connection types.
4. Click the ``Save`` button to create the connection.

Editing a Connection with the UI
--------------------------------

Open the ``Admin->Connections`` section of the UI. Click the pencil icon next
to the connection you wish to edit in the connection list.

.. image:: ../img/connection_edit.png

Modify the connection properties and click the ``Save`` button to save your
changes.

Creating a Connection with Environment Variables
------------------------------------------------

Connections in Airflow pipelines can be created using environment variables.
The environment variable needs to have a prefix of ``AIRFLOW_CONN_`` for
Airflow with the value in a URI format to use the connection properly.

When referencing the connection in the Airflow pipeline, the ``conn_id``
should be the name of the variable without the prefix. For example, if the
``conn_id`` is named ``postgres_master`` the environment variable should be
named ``AIRFLOW_CONN_POSTGRES_MASTER`` (note that the environment variable
must be all uppercase).

Airflow assumes the value returned from the environment variable to be in a URI
format (e.g.``postgres://user:password@localhost:5432/master`` or
``s3://accesskey:secretkey@S3``). The underscore character is not allowed
in the scheme part of URI, so it must be changed to a hyphen character
(e.g. `google-compute-platform` if `conn_type` is `google_compute_platform`).
Query parameters are parsed to one-dimensional dict and then used to fill extra.


.. _manage-connections-connection-types:

Connection Types
----------------

.. _connection-type-GCP:

Google Cloud Platform
~~~~~~~~~~~~~~~~~~~~~

The Google Cloud Platform connection type enables the :ref:`GCP Integrations
<GCP>`.

Authenticating to GCP
'''''''''''''''''''''

There are two ways to connect to GCP using Airflow.

1. Use `Application Default Credentials
   <https://google-auth.readthedocs.io/en/latest/reference/google.auth.html#google.auth.default>`_,
   such as via the metadata server when running on Google Compute Engine.
2. Use a `service account
   <https://cloud.google.com/docs/authentication/#service_accounts>`_ key
   file (JSON format) on disk.

Default Connection IDs
''''''''''''''''''''''

The following connection IDs are used by default.

``bigquery_default``
    Used by the :class:`~airflow.contrib.hooks.bigquery_hook.BigQueryHook`
    hook.

``google_cloud_datastore_default``
    Used by the :class:`~airflow.contrib.hooks.datastore_hook.DatastoreHook`
    hook.

``google_cloud_default``
    Used by those hooks:

    * :class:`~airflow.contrib.hooks.gcp_api_base_hook.GoogleCloudBaseHook`
    * :class:`~airflow.contrib.hooks.gcp_dataflow_hook.DataFlowHook`
    * :class:`~airflow.contrib.hooks.gcp_dataproc_hook.DataProcHook`
    * :class:`~airflow.contrib.hooks.gcp_mlengine_hook.MLEngineHook`
    * :class:`~airflow.contrib.hooks.gcs_hook.GoogleCloudStorageHook`
    * :class:`~airflow.contrib.hooks.gcp_bigtable_hook.BigtableHook`
    * :class:`~airflow.contrib.hooks.gcp_compute_hook.GceHook`
    * :class:`~airflow.contrib.hooks.gcp_function_hook.GcfHook`
    * :class:`~airflow.contrib.hooks.gcp_spanner_hook.CloudSpannerHook`
    * :class:`~airflow.contrib.hooks.gcp_sql_hook.CloudSqlHook`


Configuring the Connection
''''''''''''''''''''''''''

Project Id (optional)
    The Google Cloud project ID to connect to. It is used as default project id by operators using it and
    can usually be overridden at the operator level.

Keyfile Path
    Path to a `service account
    <https://cloud.google.com/docs/authentication/#service_accounts>`_ key
    file (JSON format) on disk.

    Not required if using application default credentials.

Keyfile JSON
    Contents of a `service account
    <https://cloud.google.com/docs/authentication/#service_accounts>`_ key
    file (JSON format) on disk. It is recommended to :doc:`Secure your connections <secure-connections>` if using this method to authenticate.

    Not required if using application default credentials.

Scopes (comma separated)
    A list of comma-separated `Google Cloud scopes
    <https://developers.google.com/identity/protocols/googlescopes>`_ to
    authenticate with.

    .. note::
        Scopes are ignored when using application default credentials. See
        issue `AIRFLOW-2522
        <https://issues.apache.org/jira/browse/AIRFLOW-2522>`_.

    When specifying the connection in environment variable you should specify
    it using URI syntax, with the following requirements:

      * scheme part should be equals ``google-cloud-platform`` (Note: look for a
        hyphen character)
      * authority (username, password, host, port), path is ignored
      * query parameters contains information specific to this type of
        connection. The following keys are accepted:

        * ``extra__google_cloud_platform__project`` - Project Id
        * ``extra__google_cloud_platform__key_path`` - Keyfile Path
        * ``extra__google_cloud_platform__key_dict`` - Keyfile JSON
        * ``extra__google_cloud_platform__scope`` - Scopes

    Note that all components of the URI should be URL-encoded.

    For example:

    .. code-block:: bash

       google-cloud-platform://?extra__google_cloud_platform__key_path=%2Fkeys%2Fkey.json&extra__google_cloud_platform__scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform&extra__google_cloud_platform__project=airflow

Amazon Web Services
~~~~~~~~~~~~~~~~~~~

The Amazon Web Services connection type enables the :ref:`AWS Integrations
<AWS>`.

Authenticating to AWS
'''''''''''''''''''''

Authentication may be performed using any of the `boto3 options <https://boto3.amazonaws.com/v1/documentation/api/latest/guide/configuration.html#configuring-credentials>`_. Alternatively, one can pass credentials in as a Connection initialisation parameter.

To use IAM instance profile, create an "empty" connection (i.e. one with no Login or Password specified).

Default Connection IDs
''''''''''''''''''''''

The default connection ID is ``aws_default``.

Configuring the Connection
''''''''''''''''''''''''''

Login (optional)
    Specify the AWS access key ID.

Password (optional)
    Specify the AWS secret access key.

Extra (optional)
    Specify the extra parameters (as json dictionary) that can be used in AWS
    connection. The following parameters are supported:

    * **aws_account_id**: AWS account ID for the connection
    * **aws_iam_role**: AWS IAM role for the connection
    * **external_id**: AWS external ID for the connection
    * **host**: Endpoint URL for the connection
    * **region_name**: AWS region for the connection
    * **role_arn**: AWS role ARN for the connection

    Example "extras" field:

    .. code-block:: json

       {
          "aws_iam_role": "aws_iam_role_name",
          "region_name": "ap-southeast-2"
       }

MySQL
~~~~~
The MySQL connection type provides connection to a MySQL database.

Configuring the Connection
''''''''''''''''''''''''''
Host (required)
    The host to connect to.

Schema (optional)
    Specify the schema name to be used in the database.

Login (required)
    Specify the user name to connect.

Password (required)
    Specify the password to connect.

Extra (optional)
    Specify the extra parameters (as json dictionary) that can be used in MySQL
    connection. The following parameters are supported:

    * **charset**: specify charset of the connection
    * **cursor**: one of "sscursor", "dictcursor, "ssdictcursor" . Specifies cursor class to be
      used
    * **local_infile**: controls MySQL's LOCAL capability (permitting local data loading by
      clients). See `MySQLdb docs <https://mysqlclient.readthedocs.io/user_guide.html>`_
      for details.
    * **unix_socket**: UNIX socket used instead of the default socket.
    * **ssl**: Dictionary of SSL parameters that control connecting using SSL. Those
      parameters are server specific and should contain "ca", "cert", "key", "capath",
      "cipher" parameters. See
      `MySQLdb docs <https://mysqlclient.readthedocs.io/user_guide.html>`_ for details.
      Note that to be useful in URL notation, this parameter might also be
      a string where the SSL dictionary is a string-encoded JSON dictionary.

    Example "extras" field:

    .. code-block:: json

       {
          "charset": "utf8",
          "cursorclass": "sscursor",
          "local_infile": true,
          "unix_socket": "/var/socket",
          "ssl": {
            "cert": "/tmp/client-cert.pem",
            "ca": "/tmp/server-ca.pem'",
            "key": "/tmp/client-key.pem"
          }
       }

    or

    .. code-block:: json

       {
          "charset": "utf8",
          "cursorclass": "sscursor",
          "local_infile": true,
          "unix_socket": "/var/socket",
          "ssl": "{\"cert\": \"/tmp/client-cert.pem\", \"ca\": \"/tmp/server-ca.pem\", \"key\": \"/tmp/client-key.pem\"}"
       }

    When specifying the connection as URI (in AIRFLOW_CONN_* variable) you should specify it
    following the standard syntax of DB connections - where extras are passed as parameters
    of the URI. Note that all components of the URI should be URL-encoded.

    For example:

    .. code-block:: bash

       mysql://mysql_user:XXXXXXXXXXXX@1.1.1.1:3306/mysqldb?ssl=%7B%22cert%22%3A+%22%2Ftmp%2Fclient-cert.pem%22%2C+%22ca%22%3A+%22%2Ftmp%2Fserver-ca.pem%22%2C+%22key%22%3A+%22%2Ftmp%2Fclient-key.pem%22%7D

    .. note::
        If encounter UnicodeDecodeError while working with MySQL connection, check
        the charset defined is matched to the database charset.

Postgres
~~~~~~~~
The Postgres connection type provides connection to a Postgres database.

Configuring the Connection
''''''''''''''''''''''''''
Host (required)
    The host to connect to.

Schema (optional)
    Specify the schema name to be used in the database.

Login (required)
    Specify the user name to connect.

Password (required)
    Specify the password to connect.

Extra (optional)
    Specify the extra parameters (as json dictionary) that can be used in postgres
    connection. The following parameters out of the standard python parameters
    are supported:

    * **sslmode** - This option determines whether or with what priority a secure SSL
      TCP/IP connection will be negotiated with the server. There are six modes:
      'disable', 'allow', 'prefer', 'require', 'verify-ca', 'verify-full'.
    * **sslcert** - This parameter specifies the file name of the client SSL certificate,
      replacing the default.
    * **sslkey** - This parameter specifies the file name of the client SSL key,
      replacing the default.
    * **sslrootcert** - This parameter specifies the name of a file containing SSL
      certificate authority (CA) certificate(s).
    * **sslcrl** - This parameter specifies the file name of the SSL certificate
      revocation list (CRL).
    * **application_name** - Specifies a value for the application_name
      configuration parameter.
    * **keepalives_idle** - Controls the number of seconds of inactivity after which TCP
      should send a keepalive message to the server.

    More details on all Postgres parameters supported can be found in
    `Postgres documentation <https://www.postgresql.org/docs/current/static/libpq-connect.html#LIBPQ-CONNSTRING>`_.

    Example "extras" field:

    .. code-block:: json

       {
          "sslmode": "verify-ca",
          "sslcert": "/tmp/client-cert.pem",
          "sslca": "/tmp/server-ca.pem",
          "sslkey": "/tmp/client-key.pem"
       }

    When specifying the connection as URI (in AIRFLOW_CONN_* variable) you should specify it
    following the standard syntax of DB connections, where extras are passed as parameters
    of the URI (note that all components of the URI should be URL-encoded).

    For example:

    .. code-block:: bash

        postgresql://postgres_user:XXXXXXXXXXXX@1.1.1.1:5432/postgresdb?sslmode=verify-ca&sslcert=%2Ftmp%2Fclient-cert.pem&sslkey=%2Ftmp%2Fclient-key.pem&sslrootcert=%2Ftmp%2Fserver-ca.pem

Oracle
~~~~~~~~
The Oracle connection type provides connection to a Oracle database.

Configuring the Connection
''''''''''''''''''''''''''
Dsn (required)
    The Data Source Name. The host address for the Oracle server.

Sid (optional)
    The Oracle System ID. The uniquely identify a particular database on a system.

Service_name (optional)
    The db_unique_name of the database.

Port (optional)
    The port for the Oracle server, Default 1521.

Login (required)
    Specify the user name to connect.

Password (required)
    Specify the password to connect.

Extra (optional)
    Specify the extra parameters (as json dictionary) that can be used in Oracle
    connection. The following parameters are supported:

    * **encoding** - The encoding to use for regular database strings. If not specified,
      the environment variable `NLS_LANG` is used. If the environment variable `NLS_LANG`
      is not set, `ASCII` is used.
    * **nencoding** - The encoding to use for national character set database strings.
      If not specified, the environment variable `NLS_NCHAR` is used. If the environment
      variable `NLS_NCHAR` is not used, the environment variable `NLS_LANG` is used instead,
      and if the environment variable `NLS_LANG` is not set, `ASCII` is used.
    * **threaded** - Whether or not Oracle should wrap accesses to connections with a mutex.
      Default value is False.
    * **events** - Whether or not to initialize Oracle in events mode.
    * **mode** - one of `sysdba`, `sysasm`, `sysoper`, `sysbkp`, `sysdgd`, `syskmt` or `sysrac`
      which are defined at the module level, Default mode is connecting.
    * **purity** - one of `new`, `self`, `default`. Specify the session acquired from the pool.
      configuration parameter.

    More details on all Oracle connect parameters supported can be found in
    `cx_Oracle documentation <https://cx-oracle.readthedocs.io/en/latest/module.html#cx_Oracle.connect>`_.

    Example "extras" field:

    .. code-block:: json

       {
          "encoding": "UTF-8",
          "nencoding": "UTF-8",
          "threaded": false,
          "events": false,
          "mode": "sysdba",
          "purity": "new"
       }

    When specifying the connection as URI (in AIRFLOW_CONN_* variable) you should specify it
    following the standard syntax of DB connections, where extras are passed as parameters
    of the URI (note that all components of the URI should be URL-encoded).

    For example:

    .. code-block:: bash

        oracle://oracle_user:XXXXXXXXXXXX@1.1.1.1:1521?encoding=UTF-8&nencoding=UTF-8&threaded=False&events=False&mode=sysdba&purity=new

Cloudsql
~~~~~~~~
The gcpcloudsql:// connection is used by
:class:`airflow.contrib.operators.gcp_sql_operator.CloudSqlQueryOperator` to perform query
on a Google Cloud SQL database. Google Cloud SQL database can be either
Postgres or MySQL, so this is a "meta" connection type. It introduces common schema
for both MySQL and Postgres, including what kind of connectivity should be used.
Google Cloud SQL supports connecting via public IP or via Cloud SQL Proxy.
In the latter case the
:class:`~airflow.contrib.hooks.gcp_sql_hook.CloudSqlDatabaseHook` uses
:class:`~airflow.contrib.hooks.gcp_sql_hook.CloudSqlProxyRunner` to automatically prepare
and use temporary Postgres or MySQL connection that will use the proxy to connect
(either via TCP or UNIX socket.

Configuring the Connection
''''''''''''''''''''''''''
Host (required)
    The host to connect to.

Schema (optional)
    Specify the schema name to be used in the database.

Login (required)
    Specify the user name to connect.

Password (required)
    Specify the password to connect.

Extra (optional)
    Specify the extra parameters (as JSON dictionary) that can be used in Google Cloud SQL
    connection.

    Details of all the parameters supported in extra field can be found in
    :class:`~airflow.contrib.hooks.gcp_sql_hook.CloudSqlDatabaseHook`

    Example "extras" field:

    .. code-block:: json

       {
          "database_type": "mysql",
          "project_id": "example-project",
          "location": "europe-west1",
          "instance": "testinstance",
          "use_proxy": true,
          "sql_proxy_use_tcp": false
       }

    When specifying the connection as URI (in AIRFLOW_CONN_* variable), you should specify
    it following the standard syntax of DB connection, where extras are passed as
    parameters of the URI. Note that all components of the URI should be URL-encoded.

    For example:

    .. code-block:: bash

        gcpcloudsql://user:XXXXXXXXX@1.1.1.1:3306/mydb?database_type=mysql&project_id=example-project&location=europe-west1&instance=testinstance&use_proxy=True&sql_proxy_use_tcp=False

SSH
~~~
The SSH connection type provides connection to use :class:`~airflow.contrib.hooks.ssh_hook.SSHHook` to run commands on a remote server using :class:`~airflow.contrib.operators.ssh_operator.SSHOperator` or transfer file from/to the remote server using :class:`~airflow.contrib.operators.ssh_operator.SFTPOperator`.

Configuring the Connection
''''''''''''''''''''''''''
Host (required)
    The Remote host to connect.

Username (optional)
    The Username to connect to the remote_host.

Password (optional)
    Specify the password of the username to connect to the remote_host.

Port (optional)
    Port of remote host to connect. Default is 22.

Extra (optional)
    Specify the extra parameters (as json dictionary) that can be used in ssh
    connection. The following parameters out of the standard python parameters
    are supported:

    * **timeout** - An optional timeout (in seconds) for the TCP connect. Default is ``10``.
    * **compress** - ``true`` to ask the remote client/server to compress traffic; `false` to refuse compression. Default is ``true``.
    * **no_host_key_check** - Set to ``false`` to restrict connecting to hosts with no entries in ``~/.ssh/known_hosts`` (Hosts file). This provides maximum protection against trojan horse attacks, but can be troublesome when the ``/etc/ssh/ssh_known_hosts`` file is poorly maintained or connections to new hosts are frequently made. This option forces the user to manually add all new hosts. Default is ``true``, ssh will automatically add new host keys to the user known hosts files.
    * **allow_host_key_change** - Set to ``true`` if you want to allow connecting to hosts that has host key changed or when you get 'REMOTE HOST IDENTIFICATION HAS CHANGED' error.  This wont protect against Man-In-The-Middle attacks. Other possible solution is to remove the host entry from ``~/.ssh/known_hosts`` file. Default is ``false``.

    Example "extras" field:

    .. code-block:: json

       {
          "timeout": "10",
          "compress": "false",
          "no_host_key_check": "false",
          "allow_host_key_change": "false"
       }

    When specifying the connection as URI (in AIRFLOW_CONN_* variable) you should specify it
    following the standard syntax of connections, where extras are passed as parameters
    of the URI (note that all components of the URI should be URL-encoded).

    For example:

    .. code-block:: bash

        ssh://user:pass@localhost:22?timeout=10&compress=false&no_host_key_check=false&allow_host_key_change=true
