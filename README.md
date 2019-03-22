<p align="center">
<img width=30% src="https://dai.lids.mit.edu/wp-content/uploads/2018/08/orion.png" alt=“Orion” />
</p>

<p align="center">
<i>Orion is a machine learning library built for telemetry data generated by satellites.</I>
</p>

<p align="center">
<i>A project from Data to AI Lab at MIT.</I>
</p>

# Orion

Orion is a machine learning library built for telemetry data generated by Satellites. With this data, our interest to develop techniques
* Identify rare patterns and flag them for expert review.
* Predict outcomes ahead of time.

The library makes use of a number of automated machine learning tools developed under
["The human data interaction project"](https://github.com/HDI-Project) within the
[Data to AI Lab at MIT](https://dai.lids.mit.edu/).

The focus, with the ready availability of automated machine learning is on:

* domain expert interaction with the machine learning system
* learning from minimal labels
* explainability of model outputs
* model audit
* scalability

## License

- MIT license

## Data Format

### Input

**Orion Pipelines** work on time Series that consist of a single table of telemetry observations
with two columns:

* `timestamp`: an INTEGER or FLOAT column with the time of the observation in
  [Unix Time Format](https://en.wikipedia.org/wiki/Unix_time)
* `value`: an INTEGER or FLOAT column with the observed value at the indicated timestamp

This is an example of such table:

|  timestamp |                value |
|------------|----------------------|
| 1222819200 | -0.3663589458809165  |
| 1222840800 | -0.3941077801118832  |
| 1222862400 |  0.4036246025342055  |
| 1222884000 | -0.36275905932000696 |
| 1222905600 | -0.3707464936807424  |

### Output

The output of the **Orion Pipelines** is another table that contains the detected anomalous
intervals and that has at least two columns:

* `start`: timestamp where the anomalous interval starts
* `end`: timestamp where the anomalous interval ends

Optionally, a third column called `score` can be included with a value that represents the
severity of the detected anomaly.

An example of such a table is:

|      start |        end |              score |
|------------|------------|--------------------|
| 1222970400 | 1222992000 | 0.5726435987608016 |
| 1223013600 | 1223035200 | 0.5726435987608016 |

## Dataset we use in this library

For development, evaluation of pipelines, we include a dataset which includes several satellite telemetry signals already formatted as expected by the Orion Pipelines.

This formatted dataset can be browsed and downloaded directly from the
[d3-ai-orion AWS S3 Bucket](https://d3-ai-orion.s3.amazonaws.com/index.html).

This dataset is adapted from the one used for the experiments in the
[Detecting Spacecraft Anomalies Using LSTMs and Nonparametric Dynamic Thresholding paper](https://arxiv.org/abs/1802.04431).
[Original source data is available for download here](https://s3-us-west-2.amazonaws.com/telemanom/data.zip).
We thank NASA for making this data available for public use.

## Orion Pipelines

The main component in the Orion project are the **Orion Pipelines**, which consist of
[MLBlocks Pipelines](https://hdi-project.github.io/MLBlocks/advanced_usage/pipelines.html)
specialized on anomaly detection on time series.

As ``MLPipeline`` instances, **Orion Pipelines**:

* consist of a list of one or more [MLPrimitives](https://hdi-project.github.io/MLPrimitives/)
* can be *fitted* on some data and later on used to *predict* anomalies on more data
* can be *scored* by comparing their predictions with some known anomalies
* have *hyperparameters* that can be *tuned* to improve their anomaly detection performance
* can be stored as a JSON file that includes all the primitives that compose them, as well as
  other required configuration options.

### Current Available Pipelines

In the **Orion Project**, the pipelines are included as **JSON** files, which can be found
inside the [orion/pipelines](https://github.com/D3-AI/Orion/tree/master/orion/pipelines) folder.

This is the list of pipelines available so far, which will grow over time:

| name | location | description |
|------|----------|-------------|
| Dummy | [orion/pipelines/dummy.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/dummy.json) | Dummy Pipeline to showcase the input and output format and the usage of sample primitives |
| LSTM Dynamic Thresholding | [orion/pipelines/lstm_dynamic_threshold.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/lstm_dynamic_threshold.json) | LSTM Based pipeline inspired by the [Detecting Spacecraft Anomalies Using LSTMs and Nonparametric Dynamic Thresholding paper](https://arxiv.org/abs/1802.04431) |
| Mean 24h LSTM | [orion/pipelines/mean_24h_lstm.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/mean_24h_lstm.json) | LSTM Based pipeline with 24h mean aggregation preprocessing |
| Median 24h LSTM | [orion/pipelines/median_24h_lstm.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/median_24_lstm.json) | LSTM Based pipeline with 24h median aggregation preprocessing |
| Sum 24h LSTM | [orion/pipelines/sum_24h_lstm.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/sum_24_lstm.json) | LSTM Based pipeline with 24h sum aggregation preprocessing |
| Skew 24h LSTM | [orion/pipelines/skew_24h_lstm.json](https://github.com/D3-AI/Orion/blob/master/orion/pipelines/skew_24_lstm.json) | LSTM Based pipeline with 24h skew aggregation preprocessing |

https://github.com/D3-AI/Orion/blob/master/orion/pipelines/mean_24h_lstm.json

### Leaderboard

In this repository we maintain the current scoring of the pipelines.
We will keep this leaderboard up-to-date.

| name | score | rank |
|------|-------|------|
| LSTM Dynamic Thresholding | 0.9623571147024044 | 1 |
| Sum 24h LSTM | 0.9540221390093286 | 2 |
| Mean 24h LSTM | 0.9536361844698463 | 3 |
| Median 24h LSTM | 0.9536361844698463 | 4 |
| Skew 24h LSTM | 0.9513204572329523 | 5 |


## Getting Started

### Requirements

#### Python

**Orion** has been developed and runs on [Python 3.6](https://www.python.org/downloads/release/python-360/).

Also, although it is not strictly required, the usage of a [virtualenv](https://virtualenv.pypa.io/en/latest/)
is highly recommended in order to avoid interfering with other software installed in the system
where **Orion** is run.

#### MongoDB

In order to be fully operational, **Orion** requires having access to a
[MongoDB](https://www.mongodb.com/) database running version **3.6** or higher.

### Install

Since **Orion** is a private project, the only way to install it is by cloning or downloading
its sources from its [GitHub repository](https://github.com/D3-AI/Orion):

```
git clone git@github.com:D3-AI/Orion.git
```

After cloning the repository and creating and activating a virtualenv, you can install
the project with this command:

```
make install
```

For development, use the following command instead, which will install some additional
dependencies for code linting and testing

```
make install-develop
```

### Quickstart: Run a Pipeline on the Demo Dataset

In the following steps we will show a short guide about how to run one of the **Orion Pipelines**
on one of the signals from the **Demo Dataset**.

**NOTE**: All the examples of this tutorial are run in an [IPython Shell](https://ipython.org/),
which you can install by running the following commands inside your *virtualenv*:

```
pip install ipython
ipython
```

#### 1. Load the data

In the first step we will load the **S-1** signal from the **Demo Dataset**,

To do so, we need to import the `orion.data.load_signal` function and call it passing
the `'S-1'` name.

```
In [1]: from orion.data import load_signal

In [2]: data = load_signal('S-1')

In [3]: data.head()
Out[3]:
    timestamp     value
0  1222819200 -0.366359
1  1222840800 -0.394108
2  1222862400  0.403625
3  1222884000 -0.362759
4  1222905600 -0.370746
```

#### 2. Detect anomalies using a pipeline

Once we have the data, use the LSTM pipeline to analyze it and search for anomalies.

In order to do so, we will have import the `orion.analysis.analyze` function and pass it
the loaded data and the path to the pipeline JSON that we want to use:

```
In [4]: from orion.analysis import analyze

In [5]: pipeline_path = 'orion/pipelines/lstm_dynamic_threshold.json'

In [6]: anomalies = analyze(pipeline_path, data)
Using TensorFlow backend.
Epoch 1/1
9899/9899 [==============================] - 55s 6ms/step - loss: 0.0559 - mean_squared_error: 0.0559
```

**NOTE:** Depending on your system and the exact versions that you might have installed
some *WARNINGS* may be printed. These can be safely ignored as they do not interfere
with the proper behavior of the pipeline.

The output of the previous command will be a ``pandas.DataFrame`` containing a table in the
Output format described above:

```
In [7]: anomalies
Out[7]:
        start         end     score
0  1398060000  1399442400  0.168381
```

#### 3. Evaluate performance

In this next step we will load some already known anomalous intervals and evaluate how
good our anomaly detection was by comparing those with our detected intervals.

For this, we will first load the known anomalies for the signal that we are using:

```
In [8]: from orion.data import load_anomalies

In [9]: truth = load_anomalies('S-1')

In [10]: truth
Out[10]:
        start         end
0  1392768000  1402423200
```

Afterwards, we pass the ground truth, the detected anomalies and the original data
to the `orion.metrics.accuracy_score` in order to compute a score that indicates
how good our anomaly detection was:

```
In [11]: from orion.metrics import accuracy_score

In [11]: accuracy_score(truth, anomalies, data)
Out[11]: 0.956346078044935
```


## Database

**Orion** comes ready to use a MongoDB Database to easily register and explore:

* Multiple Datasets based on signals from one or more satellites.
* Multiple Pipelines, including historical Pipeline versions.
* Pipeline executions on the registered Datasets, including any environment details required to
  later on reproduce the results.
* Pipeline execution results and detected events.
* Comments about the detected events.

This, among other things, allows:

* Providing visibility about the system usage.
* Keeping track of the evolution of the registered pipelines and their performance over multiple datasets.
* Visualizing and browsing the detected events by the pipelines using a web application.
* Collecting comments from multiple domain experts about the detected events to be able to later
  on curate the pipelines based on their knowledge.
* Reproducing previous executions in identical environments to replicate the obtained results.
* Detecting and keeping a history of system failures for later investigation.


### Database Schema

The **Orion Database** contains the following collections and fields:

#### Dataset

The **Dataset** collection contains all the required details to be able to load
the observations from a satellite signal, as well as some metadata about it, such as
the minimum and maximum timestamps that want to be used or the user that registered it.

##### Fields

* \_id (ObjectID): Unique Identifier of this Dataset object
* name (String): Name of the dataset
* signal_set (String): Identifier of the signal
* satellite_id (String): Identifier of the satellite
* start_time (Integer): minimum timestamp of this dataset
* stop_time (Integer): maximum timestamp of this dataset
* data_location (String): URI of the dataset
* timestamp_column (Integer): index of the timestamp column
* value_column (Integer): index of the value column
* created_by (String): Identifier of the user that created this Dataset Object
* insert_time (DateTime): Time when this Dataset Object was inserted

#### Pipeline

The **Pipeline** collection contains all the pipelines registered in the system, including
their details, such as the list of primitives and all the configured hyperparameter values.

##### Fields

* \_id (ObjectID): Unique Identifier of this Pipeline object
* name (String): Name given to this pipeline
* mlpipeline (SubDocument): JSON representation of this pipeline
* created_by (String): Identifier of the user that created this Pipeline Object
* insert_time (DateTime): Time when this Pipeline Object was inserted

#### Datarun

The **Datarun** objects represent single executions of a **Pipeline** on a **Dataset**,
and contain all the information about the environment and context where this execution
took place, which potentially allows to later on reproduce the results in a new environment.

It also contains information about whether the execution was successful or not, when it started
and ended, and the number of events that were found by the pipeline.

##### Fields

* \_id (ObjectID): Unique Identifier of this Datarun object
* dataset_id (ObjectID - Foreign Key): Unique Identifier of the Dataset used
* pipeline_id (ObjectID - Foreign Key): Unique Identifier of the Pipeline used
* start_time (DateTime): When the execution started
* end_time (DateTime): When the execution ended
* software_versions (List of Strings): version of each python dependency installed in the
  *virtualenv* when the execution took place
* budget_type (String): Type of budget used (time or number of iterations)
* budget_amount (Integer): Budget amount
* model_location (String): URI of the fitted model
* metrics_location (String): URI of the metrics
* events (Integer): Number of events detected during this Datarun execution
* status (String): Whether the Datarun is still running, finished successfully or failed
* created_by (String): Identifier of the user that created this Datarun Object
* insert_time (DateTime): Time when this Datarun Object was inserted

#### Event

Each one of the anomalies detected by the pipelines is stored as an **Event**, which
contains the details about the start time, the stop time and the severity score.

##### Fields

* \_id (ObjectID): Unique Identifier of this Event object
* datarun_id (ObjectID - Foreign Key): Unique Identifier of the Datarun during which this
  Event was detected.
* start_time (Integer): Timestamp where the anomalous interval starts.
* stop_time (Integer): Timestamp where the anomalous interval ends.
* score (Float): Severity score given by the pipeline to this Event
* tag (String): User given tag for this Event
* insert_time (DateTime): Time when this Event Object was inserted

#### Comment

Each Event can have multiple **Comments**, from one or more users.
**Comments** are expected to be inserted by the domain experts after the Datarun has
finished and they analyze the results.

##### Fields

* \_id (ObjectID): Unique Identifier of this Comment object
* event_id (ObjectID - Foreign Key): Unique Identifier of the Event to which this Comment relates
* text (String): Comment contents
* created_by (String): Identifier of the user that created this Event Object
* insert_time (DateTime): Time when this Event Object was inserted


### Database Usage

In order to make **Orion** interact with the database you will need to use the `OrionExplorer`,
which provides all the required functionality to register and explore all the database objects,
as well as load pipelines and datasets from it in order to start new dataruns and detect events.

In the following steps we will go over a typical session using the `OrionExplorer` to:
* register a new dataset
* register a new pipeline
* create a datarun using the pipeline to detect events on the dataset
* explore the detected events
* add some comments to the detected events

Note that, because of the dynamic schema-less nature of MongoDB, no database initialization
or table creation is needed. All you need to do start using a new database is create the
`OrionExplorer` instance with the right connection details and start using it!

#### 1. Connecting to the Database

In order to connect to the database, all you need to do is import and create an instance of the
`OrionExplorer`.

```
In [1]: from orion.explorer import OrionExplorer

In [2]: orex = OrionExplorer()
```

This will directly create a connection to the database named `'orion'` at the default
MongoDB host, `localhost`, and port, `27017`.

In case you want to connect to a different database, host or port, or in case user authentication
is enabled in your MongoDB instance, you can pass any required additional arguments:

* `database`
* `host`
* `port`
* `username`
* `password`
* `authentication_source`

```
In [3]: orex = OrionExplorer(
   ...:     database='orion_database',
   ...:     host='1.2.3.4',
   ...:     port=1234,
   ...:     username='orion',
   ...:     password='secret_password',
   ...:     authentication_source='admin'
   ...: )
```

#### 2. Registering a new Dataset

The first thing that you will need to do to start using **Orion** with a Database will be
**registering a new dataset**.

For this you will need to call the `OrionExplorer.add_dataset` method passing:

* `name - str`: Name that you want to give to this dataset. It must be unique.
* `signal_set - str`: Identifier of the signal(s).
* `satellite_id - str`: (Optional) Identifier of the satellite.
* `start_time - int`: (Optional) minimum timestamp to be used for this dataset. If not given, it
  defaults to the minimum timestamp found in the data.
* `stop_time - int`: (Optional) maximum timestamp to be used for this dataset. If not given, it
  defaults to the maximum timestamp found in the data.
* `location - int`: (Optional) path to the CSV. Skip if using a demo signal hosted on S3.
* `timestamp_column - int`: (Optional) index of the timestamp column. Defaults to 0.
* `value_column - int`: (Optional) index of the value column. Defaults to 1.
* `user - str`: (Optional) Identifier of the user that is creating this Dataset.

In the simplest scenario, you can register a dataset that uses one of the demo signals with the
default start and stop times.

For example, to register a dataset for the signal **S-1** used previously you will execute:

```
In [4]: orex.add_dataset('S-1', 'S-1')
```

In a more complex scenario, we might be loading a CSV file that has the time index in the
third column and the value in the fifth one, and we might want to restrict the timestamps
to a certain range and provide the ID of the user that is registering it.

In this case we will execute:

```
In [5]: orex.add_dataset(
   ...:     name='a_dataset',
   ...:     signal_set='a_signal_name',
   ...:     satellite_id='a_satellite_id',
   ...:     start_time=1483228800,
   ...:     stop_time=1514764800,
   ...:     location='/path/to/the.csv',
   ...:     timestamp_column=2,
   ...:     value_column=4,
   ...:     user_id='1234',
   ...: )
```

**NOTE**: The dataset name must be unique, which means that `add_dataset` method will fail
if a second dataset is added using the same name.

#### 3. Exploring the registered datasets

In order to obtain the list of already registered datasets, you can use the
`OrionExplorer.get_datasets` method.

This method returns a `pandas.DataFrame` containing all the details about the registered
datasets:

```
In [6]: datasets = orex.get_datasets()

In [7]: datasets[['name', 'signal_set', 'start_time', 'stop_time']]
Out[7]:
        name     signal_set  start_time   stop_time
0        S-1            S-1  1222819200  1442016000
1  a_dataset  a_signal_name  1483228800  1514764800
```

Optionally, you can restrict the results to a particular signal or satellite by passing them
as arguments to the `get_datasets` method:

```
In [8]: datasets = orex.get_datasets(signal='S-1')

In [9]: datasets[['name', 'signal_set', 'start_time', 'stop_time']]
Out[9]:
  name signal_set  start_time   stop_time
0  S-1        S-1  1222819200  1442016000
```

#### 4. Registering a new Pipeline

Another thing that you will need to do before being able to process the created dataset will be
**registering a new pipeline**.

For this you will need to call the `OrionExplorer.add_pipeline` method passing:

* `name - str`: Name that you want to give to this pipeline.
* `path - str`: Path to the JSON file that contains the pipeline specification.
* `user - str`: (Optional) Identifier of the user that is creating this Pipeline.

For example, to register the LSTM pipeline that we used previously under the `'LSTM'` name
you will execute:

```
In [10]: orex.add_pipeline('LSTM', 'orion/pipelines/lstm_dynamic_threshold.json')
```

**NOTE**: In this case the name of the pipeline does not need to be unique. If a pipeline is
added more than once using the same name, they will be stored independently and considered
different versions of the same pipeline.


#### 5. Exploring the registered pipelines

Just like datasets, you can obtain the list of registered pipelines using the
`OrionExplorer.get_pipelines` method.

This method returns a `pandas.DataFrame` containing all the details about the registered
pipelines:

```
In [11]: pipelines = orex.get_pipelines()

In [12]: pipelines[['pipeline_id', 'name', 'insert_time']]
Out[12]:
                pipeline_id  name             insert_time
0  5c92797a6c1cea7674cf5b48  LSTM 2019-03-20 17:33:46.452
```

#### 6. Running a pipeline on a dataset

Once we have at least one dataset and one pipeline registered, you can start analyzing the data
in search for anomalies.

In order to do so, all you need to do is call the `OrionExplorer.analyze` method passing
the name of pipeline and the name of the dataset:

```
In [13]: datarun = orex.analyze('S-1', 'LSTM')
Using TensorFlow backend.
Epoch 1/1
9899/9899 [==============================] - 55s 6ms/step - loss: 0.0561 - mean_squared_error: 0.0561
```

Once the process has finished, a new Datarun object will have been created in the Database
and returned.

```
In [14]: datarun.id
Out[14]: ObjectId('5c927a846c1cea7674cf5b49')

In [15]: dataruns = orex.get_dataruns()

In [16]: dataruns[['datarun_id', 'start_time', 'end_time', 'events']]
Out[16]:
                 datarun_id               start_time                end_time  events
0  5c927a846c1cea7674cf5b49  2019-03-20 17:38:12.133 2019-03-20 17:39:36.279       2
```

#### 7. Explore the found Events

A part from visualizing the number of Events found during the pipeline execution, we will want
to see the exact details of each event.

As you might already be guessing, this can be obtained by calling the `OrionExplorer.get_events`
method and passing it the Datarun object returned by the `analyze` method:

```
In [17]: events = orex.get_events(datarun)

In [18]: events[['event_id', 'score', 'start_time', 'stop_time', 'comments']]
Out[18]:
                   event_id     score  start_time   stop_time  comments
0  5c927ad86c1cea7674cf5b4a  0.047956  1398340800  1398621600         0
1  5c927ad86c1cea7674cf5b4b  0.120997  1398686400  1399420800         0
```

Alternatively, the Datarun ID can be passed directly:

```
In [19]: events = orex.get_events('5c927a846c1cea7674cf5b49')

In [20]: events[['event_id', 'score', 'start_time', 'stop_time', 'comments']]
Out[20]:
                   event_id     score  start_time   stop_time  comments
0  5c927ad86c1cea7674cf5b4a  0.047956  1398340800  1398621600         0
1  5c927ad86c1cea7674cf5b4b  0.120997  1398686400  1399420800         0
```

#### 8. Add comments to the Events

While visualizing the detected Events, you might want to add some comments about them.

This is done using the `OrionExplorer.add_comment` method, which will expect:
* The Event object or its ID
* The comment text
* The user ID

```
In [21]: orex.add_comment('5c927ad86c1cea7674cf5b4a', 'This needs to be further investigated', '1234')

In [22]: orex.add_comment('5c927ad86c1cea7674cf5b4b', 'This is probably a false positive', '1234')
```

#### 9. Retrieving the Event comments

After adding some comments, these can be recovered using the `OrionExplorer.get_comments`.

This method accepts as optional arguments the Datarun or the Event ID in order to filter
the results:

```
In [23]: comments = orex.get_comments('5c927a846c1cea7674cf5b49')

In [24]: comments[['event_id', 'created_by', 'insert_time', 'text']]
Out[24]:
                   event_id created_by             insert_time                                   text
0  5c927ad86c1cea7674cf5b4a       1234 2019-03-21 13:06:33.591  This needs to be further investigated
1  5c927ad86c1cea7674cf5b4b       1234 2019-03-21 13:07:08.935      This is probably a false positive
```
