> You can download the code for
<a href="./../src/step_02.py" download>Step 2</a> 
or all the steps <a href="./../src/src.zip" download>here</a>. 

# Basic functions

*Estimated Time for Completion: 15 minutes; Difficulty Level: Beginner*

Let's discuss some of the essential functions that come along with Taipy.

- [`<Data Node>.write(<new value>)`](../../../../manuals/core/entities/data-node-mgt.md/#read-write-a-data-node): this instruction changes the data of a Data Node.

- [`tp.get_scenarios()`](../../../../manuals/core/entities/scenario-cycle-mgt.md/#get-all-scenarios): this function returns the list of all the scenarios.

- [`tp.get(<Taipy object ID>)`](../../../../manuals/core/entities/data-node-mgt.md/#get-data-node): this function returns an entity based on the id of the entity.

- [`tp.delete(<Taipy object ID>)`](../../../../manuals/core/entities/scenario-cycle-mgt.md/#delete-a-scenario): this function deletes the entity and nested elements based on the id of the entity.

## Utility of having scenarios

Using Taipy, users can create multiple copies of the same setup. These copies can have different data. 
Understanding these differences in data between the copies is important. 
These variations in scenarios can happen because of the following reasons:

- Changing data from input data nodes, 

- Randomness in a task (random algorithm), 

- Different values from parameters set by the end-user, etc.

The developer has the ability to directly modify the data nodes entities using the _write_ function (as shown below).

![](config_02.svg){ width=700 style="margin:auto;display:block;border: 4px solid rgb(210,210,210);border-radius:7px" }

```python
scenario = tp.create_scenario(scenario_cfg, name="Scenario")
tp.submit(scenario)
print("Output of First submit:", scenario.output.read())
```

Results:

```
[2022-12-22 16:20:02,874][Taipy][INFO] job JOB_double_a5ecfa4d-1963-4776-8f68-0859d22970b9 is completed.
Output of First submit: 42
```

## _write_ function

Data of a Data Node can be changed using _write_. The syntax is `<Scenario>.<Data Node>.write(value)`.


```python
print("Before write", scenario.input.read())
scenario.input.write(54)
print("After write",scenario.input.read())
```

Results:
```
Before write 21
After write 54
```

The submission of the scenario updates the output values.


```python
tp.submit(scenario)
print("Second submit",scenario.output.read())
```

Results:
```
[2022-12-22 16:20:03,011][Taipy][INFO] job JOB_double_7eee213f-062c-4d67-b0f8-4b54c04e45e7 is completed.
Second submit 108
```
    
## Other useful functions

- `tp.get_scenarios` accesses all the scenarios by returning a list.

```python
print([s.name for s in tp.get_scenarios()])
```

Results:
```
["Scenario"]
```

- Get an entity from its id:

```python
scenario = tp.get(scenario.id)
```

- Delete an entity through its id. For example, to delete a scenario:

```python
tp.delete(scenario.id)
```

## Ways of executing the code: Versioning

Taipy provides a [versioning system](../../../../manuals/core/versioning/index.md) to 
keep track of the changes that a configuration experiences over time: new data 
 sources, new parameters, new versions of your Machine Learning engine, etc. 
 `python main.py -h` opens a helper to understand the versioning options at your disposal.

# Entire code

```python
from taipy import Config
import taipy as tp


def double(nb):
    return nb * 2

# Configuration in Python
input_data_node_cfg = Config.configure_data_node("input", default_data=21)
output_data_node_cfg = Config.configure_data_node("output")

task_cfg = Config.configure_task("double",
                                 double,
                                 input_data_node_cfg,
                                 output_data_node_cfg)

scenario_cfg = Config.configure_scenario(id="my_scenario",
                                                    task_configs=[task_cfg])


if __name__ == '__main__':
    tp.Core().run()

    scenario = tp.create_scenario(scenario_cfg, name="Scenario")
    tp.submit(scenario)
    print("Output of First submit:", scenario.output.read())

    print("Before write", scenario.input.read())
    scenario.input.write(54)
    print("After write",scenario.input.read())


    tp.submit(scenario)
    print("Second submit",scenario.output.read())

    # Basic functions of Taipy Core 
    print([s.name for s in tp.get_scenarios()])
    scenario = tp.get(scenario.id)
    tp.delete(scenario.id)

    scenario = None
    data_node = None

    tp.Gui("""<|{scenario}|scenario_selector|>
              <|{scenario}|scenario|>
              <|{scenario}|scenario_dag|>
              <|{data_node}|data_node_selector|>""").run()
```

