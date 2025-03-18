# Contribution to SmartDelta project

# FineCatIss

FineCatIss is a pipeline built upon the [CatIss tool](https://github.com/MalihehIzadi/catiss).
The pipeline gathers all issue data of a given GitHub repository. This step can be skipped by giving a path to the `.json` file containing all necessary data in the `repo_data_config.toml`. Afterwards the data is preprocessed to prepare it for the model, then the model is fine-tuned on already labeled issue data and finally the fine-tuned model is used to make predictions for unlabeled issues. Predictions can optionally be exported as `.csv` or directly added to the GitHub repositories issues if given a connection that allows so.

## How to use it

Make sure to install all requirements using `pip install requirements.txt`. (`requirementsV2.txt` is created using conda)!
1. Generate a GitHub Access token with at least read permissions (also write if you want to update labels of issues of your GitHub repo) and add it to the .env file: GITHUB_PAT=`[YOUR_GITHUB_PAT]`
2. Download the CatIss model [pytorch_model.bin](https://drive.google.com/drive/folders/1jgV4U41-2acctpc6jH5DWL3fF5V6bKF8?usp=sharing) into the model folder.
3. If the model filename differs from `pytorch_model.bin`, update the `pretrained_model_file_name` field in the `ml_config.toml`. If the folder name differs make sure to adapt `config.toml` accordingly.
4. Set `user_name` and `repo_name` in `config.toml`.
5. (Optional) Set `repo_data_file` in the `repo_data_config.toml`. If not specified, the tool will default to looking for and saving the data as `f"{user_name}_{repo_name}_repo_data.json"` in the `data` folder, which can be adapted in `config.toml`.
    - 4a. Let our script fetch all issue data of your repository. This can be very time-consuming if you have a large repository.
    - 4b. Fetch all issue data on your own and save it to a `.json` file.
6. Adapt `label_config.toml` to match your repository's labels.
7. (Optional) Adapt other configs. Details on all configs below.
8. Run Jupyter notebook in the notebooks folder

## Issue data

Below all keys of our issue data dictionary. As the `Used?` column shows, keys with quotation marks aren't used by this tool, but only our [Visualization Tool](). All keys used can be adapted in the `ml_config.toml`.

| Key              | Description                               | Used?     |
|------------------|-------------------------------------------|----------------|
| `number`        | Issue number                                 | ✅ Yes &nbsp;|
| `title`         | Issue title                                  | ✅ Yes &nbsp;|
| "state"         | Open or closed                               | ❌ No &nbsp;|
| "created_at"    | Issue creation timestamp                    | ❌ No &nbsp;|
| "closed_at"     | Issue closing timestamp (if applicable, otherwise: None)     | ❌ No &nbsp;|
| "user_name"     | Issue creator's name                        | ❌ No &nbsp;|
| "comments"      | Number of comments on the issue             | ❌ No &nbsp;|
| `body`          | Issue description                           | ✅ Yes &nbsp;|
| "description_length" | Length of issue description            | ❌ No &nbsp;|
| `label`         | Labels of issue                             | ✅ Yes &nbsp;|
| `predicted_label` | Predicted label by our tool (default: "NONE") | ✅ Yes &nbsp;|
| `author`        | Author association of issue creator        | ✅ Yes &nbsp;|

## Configs

Below `[project_folder]` is referring to the folder in which the repository got cloned into.

### config.toml

In this config user name and repository name of the GitHub repository have to be set. Here you also can disable fetching new issues for every run.
This config contains variables to allow changes in folder structure and file names for other configs as well as the name and output file for the logger.

### logging_config.yaml

Your typical logging config.

### repo_data_config.toml

Config to specify path to `.json file` containing repositories issue data. Default path is `[project_folder]_[data_folder]_{user_name}_{repository_name}_repo_data.json`. Default path doesn't have to be specified.


### label_config.toml

Label config is the most important one. Here all bug, enhancement and support labels need to specified. Custom prediction labels can be specified here as well. Those are only used if issues are updated in the GitHub repository, which requires a GitHub connection with write permissions.

### ml_config.toml

It is necessary to have the pre-trained CatIss model in the folder: `[project folder]/[model folder]` and pretrained_model_file_name has to be set to it's file name.
Evaluation can be turned on or off in this config. And if evaluation is turned on the split size between train and validation split, as well as a random state to always ensure same splits, can be set here.
Furthermore this config is used to change the names of the keys of the data dictionary as well as adapting data exports. In cases where the repo data dict json containing all the required issue data is generated using something else than our tool, please ensure that all those fields are available and adapt the keys in the config in case they differ from ours.
Please note that predicted_labels_col should always be None at the start, as our tool only makes predictions if that value is None. Currently it is required and doesn't get added by our tool!