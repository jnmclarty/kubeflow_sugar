# Kubeflow Sugar

A dependency-optional python library which reduces boilerplate and helps make any KFP SDK ("kfp") compatible code less verbose.

So far, kfp enables pipeline development and surrounding workflows, but expects compatibility with in-container code via a documentation-driven-protocol.  This tool focuses on strengthening that protocol, mostly from the in-container-code and post-pipeline-line-run side. In general, kfp is imported by pipeline builders.  In general, kubeflow_sugar would be imported by in-container code.

## Features
None yet, PRs incoming...

## Coming Soon
* Pythonic (ie JSON-free & OO) metric capture (ie `/mlpipeline-metrics.json`)
* Pythonic (ie JSON-free & OO) UI artifact capture (ie `/mlpipeline-ui-metadata.json`)
* Environment-variable based pipeline-to-container-code coupling (and boiler plate reduction) for dynamic filenaming of the above. 
* In-Pipeline vs Out-Of-Pipeline awareness flag.
* Lower-Mouse-Click way to sync KFP step input and output from one step, to another python interpreter (For QA, debugging & inspecting)

### Example of Object Oriented Metric Capture

Instead of...

```
  num_cnt = len(some_list)
  ...
  accuracy = accuracy_score(df['target'], df['predicted'])
  ...
  metrics = {
    'metrics': [{
      'name': 'accuracy-score', # The name of the metric. Visualized as the column name in the runs table.
      'numberValue':  accuracy, # The value of the metric. Must be a numeric value.
      'format': "PERCENTAGE",   # The optional format of the metric. Supported values are "RAW" (displayed in raw format) and "PERCENTAGE" (displayed in percentage format).
    },{
      'name': 'number-of-things',
      'numberValue':  num_cnt,
      'format': "RAW",
    }]
  }
  with file_io.FileIO('/mlpipeline-metrics.json', 'w') as f:
    json.dump(metrics, f)
```

You could write...

```
from kubeflow_sugar import PipelineMetricCollector, PERCENT

collector = PipelineMetricCollector('mlpipeline-metrics.json')

collector.collect(number_of_things=len(some_list))

accuracy = accuracy_score(df['target'], df['predicted'])
collector.collect(accuracy_score=accuracy, type=PERCENT)
```
then bonus points for...
```
metrics = pd.Series(...)
collector.collect(metrics)

# or

metrics = dict(metric_one=x, metric_two=y)
collector.collect(metrics)
```

### Example of Pipeline-Run-Check

```
from kubeflow_sugar import is_pipeline_run


if is_pipeline_run():
  file_save_location = '/mnt'
else:
  file_save_location = '~/data'
```

### Example of Pipeline-Run-Check

```
from kubeflow_sugar import is_pipeline_run


if is_pipeline_run():
  file_save_location = '/mnt'
else:
  file_save_location = '~/data'
```


# Why not send PRs to the KFP SDK?

This library is intended to function without needing kfp installed - for the most part.  In other places, consider it a (non graphical) UI to pipeline related information.  You shouldn't need kfp (and it's dependencies) in any business-logic/modelling code.  But, this library can be imported anywhere without bringing all the kfp dependencies.

