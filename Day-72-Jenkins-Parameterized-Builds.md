# Day 72: Jenkins Parameterized Builds

## Challenge Description
The goal of this challenge is to create a simple parameterized Jenkins job named `parameterized-job` to understand the basic functionality of parameterized builds. The job includes a string parameter and a choice parameter, and executes a shell command to echo their values.

## Key Concepts
*   **Jenkins Parameterized Build:** Allows users to pass parameters (string, choice, boolean, etc.) to a job before execution.
*   **String Parameter:** A simple text input parameter.
*   **Choice Parameter:** A dropdown menu parameter allowing users to select from a predefined list of options.
*   **Execute Shell:** A build step in Jenkins that runs shell commands in the workspace.

## Solution

1.  **Access Jenkins:**
    *   We logged in to the Jenkins UI using the credentials `admin` / `Adm!n321`.

2.  **Create the Job:**
    *   We created a new **Freestyle project** named `parameterized-job`.

3.  **Configure Parameters:**
    *   In the **General** section, we enabled **This project is parameterized**.
    *   **String Parameter:**
        *   Name: `Stage`
        *   Default Value: `Build`
    *   **Choice Parameter:**
        *   Name: `env`
        *   Choices:
            *   `Development`
            *   `Staging`
            *   `Production`

4.  **Configure Build Step:**
    *   In the **Build** section, we added an **Execute shell** step with the following command:
        ```bash
        echo "Current Stage is: $Stage"
        echo "Current Environment is: $env"
        ```

5.  **Save:**
    *   We saved the configuration.

## Verification

1.  **Run the Build:**
    *   We selected **Build with Parameters**.
    *   We kept the default value for `Stage` (`Build`).
    *   We selected `Staging` from the `env` dropdown.
    *   We clicked **Build**.

2.  **Check Console Output:**
    *   We verified the console output of the build.
    *   The output correctly echoed the parameter values and finished with `SUCCESS`:
        ```text
        + echo 'Current Stage is: Build'
        Current Stage is: Build
        + echo 'Current Environment is: Staging'
        Current Environment is: Staging
        Finished: SUCCESS
        ```
