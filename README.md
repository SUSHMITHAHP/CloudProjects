# CloudProjects

# Project Title: ML22/23-4 Investigate and Implement KNN Classifier 

## Table of Contents

1. [Introduction](#introduction)
2. [Goal of the experiment](#goal-of-the-experiment)
3. [Prerequisites - SE Project Dependencies](#prerequisites---se-project-dependencies)
4. [Important Project Links](#important-project-links)
5. [Technologies Used](#technologies-used)
6. [Key Features](#key-features)
7. [Project Architecture](#project-architecture)
8. [Project Functionality Overview](#project-functionality-overview)
   - [Input Parameters](#input-parameters)
   - [Algorithm Used for Predicting Input Values from SDR by KNN](#algorithm-used-for-predicting-input-values-from-sdr-by-knn)
   - [Output Parameters](#output-parameters)
9. [Azure Deployment Details](#azure-deployment-details)
10. [Experiment Result Analysis](#experiment-result-analysis)
      - [Input Files Given to the Blob Storage](#input-files-given-to-the-blob-storage)
      - [Message Queues](#message-queues)
      - [Output Files Generated from the Blob Storage](#output-files-generated-from-the-blob-storage)
      - [Output Tables Generated upon Successful Run](#output-tables-generated-upon-successful-run)
      - [Illustration of the result visualization](#illustration-of-the-result-visualization)
      - [Result Inference](#result-inference)
11. [Guide to Run the Project in Cloud](#guide-to-run-the-project-in-cloud)
12. [Future Scope](#future-scope)
13. [Conclusion](#conclusion)
14. [References](#references)


## Introduction:
This project implements a K-Nearest Neighbor (KNN) classifier integrated with the NeoCortex API. The classifier processes sequences with associated labels for training and predicts classifications for test sequences. 

The solution has been containerized using Docker and deployed on Microsoft Azure, incorporating services like Azure Container Instances and Azure Blob Storage for scalability and data management. The system supports two prediction algorithms (SoftMax and Simple Weight-based) and allows users to select the desired approach via a queue message flag. This document provides a step-by-step guide for deploying, running, and extending the project.

## Goal of the experiment:

This project is designed to predict input values using a K-Nearest Neighbors (KNN) classifier based on Sparse Distributed Representations (SDR). It operates by triggering the `MultiSequenceLearning` experiment within a cloud environment. The purpose of the classifier is to convert Sparse Distributed Representations (SDR) data and compare it with the original input format. The experimental workflow is shown below:

<p align="center">
  <img src="SE project workflow.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 1: SE Project Workflow</em>
</p>

As described in figure 1, the workflow begins with raw data being processed by an Encoder, which converts it into encoded data. This encoded data is then passed through an HTM classifier, where it is transformed into Sparse Distributed Representations (SDR). The KNN classifier utilizes the SDR data for classification tasks, ensuring accurate pattern recognition. Finally, the reconstructed data is compared with the original raw data to assess the effectiveness and accuracy of the process.

- **Prediction Process**: The `KNNClassifier.cs` utilizes the `GetPredictedInputValues` method to forecast the next set of input values after processing input sequences. The aim is to verify the classifier's accuracy by comparing the original input data with the reconstructed data from the KNN model. 
- **Prediction Algorithms**: Two prediction approaches are supported - **SoftMax**, a probabilistic approach that computes probabilities for each possible outcome, and **Simple Weight-based Algorithm**, a straightforward method relying on weighted inputs. The choice of algorithm is determined by a boolean flag passed in the queue.
- **Result Storage**: Output data is saved in a blob container (outputfile), and experiment parameters and metadata are logged in an output table for easy retrieval and analysis.

This modular setup enables developers to implement predictive modelling efficiently in a cloud environment while offering customizable prediction strategies.

## Prerequisites - SE Project Dependencies
To set up the project, ensure the following steps are completed:

#### 1. Install NeoCortex API

- Add the NeoCortex API via NuGet in Visual Studio.

#### 2. Add Custom Code
- Include custom files (IClassifier1.cs, MultiSequenceLearning1.cs, Predictor1.cs) in the MyExperiment folder of the project.

#### 3. Avoid Conflicts
- Rename custom files properly to prevent name conflicts with existing NeoCortex API files.
  
#### 4. Directory Structure
- Maintain an organized folder structure for easy navigation and troubleshooting throughout the project.
  
## Important Project Links

### SE Project Links
1. [Paper](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/MySEProject/Implement%20and%20investigate%20KNN%20-%20rookie_developer.pdf)
2. [ReadME](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/MySEProject/readme.md)
3. [Link to SE Project Submission Issue](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/issues/35)
4. [KNNClassifier.cs](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs)
5. [MultisequenceLearning.cs](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/Samples/NeoCortexApiSample/MultisequenceLearning.cs)

Implemented Methods in SE project:

| **Approach 1: Simple Weightage Algorithm** |  |
| --- | --- |
| **Prediction and Weightage Methods** | [GetPredictedInputValues](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L278), [SelectBestClassification](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L444) |
| **Metrics Available** | [LeastValue](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L329), [ComputeCosineSimilarity](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L374) |
| **Distance Table Methods** | [GetDistanceTable](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L348), [GetDistanceTableforCosine](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L403) |

| **Approach 2: SoftMax Algorithm**          |  |
| --- | --- |
| **Prediction and Weightage Methods**       | [PredictWithSoftmax](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L555), [CalculateSoftmaxWeights](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L648) |
| **Metrics Available** | [ComputeCosineSimilarity](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L374) |
| **Distance Table Method** | [GetDistanceTableforCosine](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L403)  |
| **Probability Method** | [Softmax](https://github.com/IndranilSaha09/neocortexapi/blob/master/source/NeoCortexApi/Classifiers/KnnClassifier.cs#L620) |

 ### Cloud Project Links

1. [MyCloudProject](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/tree/rookie_developers/Source/MyCloudProjectSample/MyCloudProject)
2. [MyExperiment](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/tree/rookie_developers/Source/MyCloudProjectSample/MyExperiment)
3. [MyCloudProject.Common](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/tree/rookie_developers/Source/MyCloudProjectSample/MyCloudProject.Common)
4. [Experiment.cs](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/MyExperiment/Experiment.cs)
5. [ReadMe](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/Documentation/Investigate%20and%20Implement%20KNN%20classifier_ReadME.md)
 
Note: Above mentioned links and documentations shall provide a better understanding about the project structure.

## Technologies Used:
- Programming Language: C#
- Containerization: Docker
- Cloud Platform: Microsoft Azure

## Key Features
- [x] **Microsoft Azure Integration:**  
  Deployed on Azure for robust cloud infrastructure, ensuring high availability and scalability for the Software Engineering project.
- [x] **Docker for Containerization:**  
  Utilized Docker to package application, providing consistent environments and simplifying deployment process.
- [x] **KNN Classifier experimented with multiple algorithms and inputs:**  
  Supports large datasets (1,000+ values) for efficient and accurate data classification with prediction based on SoftMax and LeastValue algorithm.
- [x] **Azure Container Registry:**  
  Secure storage for Docker images, enabling easy management and streamlined Azure deployments.
- [x] **Azure Container Instances:**  
  Deploys Docker containers in scalable, isolated cloud environments without managing VMs.
- [x] **Comprehensive Azure Storage:**  
  Efficient data management with Blob, Queue, and Table storage for various data types.
- [x] **Monitoring and Logging:**  
  Real-time insights via Azure Monitor and Log Analytics for performance tracking and issue resolution.

## Project Architecture:
This section showcases the architecture diagram of the project, highlighting the key components that are crucial to its structure and functionality.
<p align="center">
  <img src="image 1.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 2: Project Architecture</em>
</p>

1. **Visual Studio**: Integrated Development Environment used for code development and debugging.
2. **GitHub**: Used for the version control and collaborative software development processes.
3. **Docker**: Used as a containerization platform for packaging of application in the form of image for Azure deployment.
4. **Docker Image**: Lightweight executable package that includes the necessary code for deployment.
5. **Azure Container Instances**: A serverless container service in Azure where the application has been deployed and ready for user access.
6. **Azure Container Registry**: This is the docker container registry service storing the docker images.
7. **Azure Storage**:

   a. **Input and Output Containers**: Two containers are created one is inputfile container which will use to give the input data to aapplication and the second one is output container where the app is storing the output in text format.

   b. **Queue**: A message queue is use to trigger the application so that application will start based on received queue message.

   c. **Table**: In the table, the experiment execution data is stored. This is part of output result.

## Project Functionality Overview:

This functional diagram outlines the cloud-based workflow for running a KNN Classifier experiment using Azure services.

1. **User Initiation**: The experiment begins when the user sends a message to the `knnqueue`. The message contains critical details such as:
   - `ExperimentId`
   - `Name`
   - `Description`
   - `InputFile`
   - A boolean flag (`useSoftmax`) to select the prediction method (Softmax or Simple weight-based).

2. **Container Instance Activation**: Once the message is received by `knnqueue`, an Azure Container Instance is automatically triggered to process the experiment.

3. **Data Handling**:
   - **Input Data**: Training and testing sequences are stored in an Azure Storage Container under `InputFile`.
   - The Azure Container Instance processes this data and, depending on the `useSoftmax` flag, applies the corresponding algorithm to predict the next input values using the KNN Classifier.

4. **Output and Storage**:
   - **Output Data**: Upon completion of the experiment, the results are stored in an Azure Storage Container under `OutputFile`.
   - **Metadata Recording**: Experiment metadata, including `ExperimentId`, timestamp, and parameters, is logged in Azure Table Storage under `Output Table`.

This setup ensures efficient execution and management of machine learning experiments within the Azure cloud environment, allowing for easy access to both the experiment results and metadata.



<p align="center">
  <img src="image 2.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 3: Functional Diagram of the implementation</em>
</p>


### Input Parameters:
As shown in Figure 2, user can push the message "Train_Test_sequence.txt" into the "inputfile" Storage Container and the request message to "knnqueue" respectively. The extraction of the details from the "knnqueue" message is performed by "ExperimentRequestMessage" class in "MyExperiment". Code snippet for "ExperimentRequestMessage" class is provided below for reference -

ExperimentRequestMessage Class

```csharp
 internal class ExerimentRequestMessage : IExerimentRequest
    {
        public string ExperimentId { get; set; }
        public string InputFile { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public string MessageId { get; set; }
        public string MessageReceipt { get; set; }
        public bool useSoftmax { get; set; }
    }
```

In addition to it, "Train_Test" Class is implemented to read JSON data and split it.

```csharp
namespace MyExperiment
{
    public class Train_Test
    {
        public Dictionary<string, List<double>> Train { get; set; } = new();

    }
}
```
### Algorithm Used for Predicting Input Values from SDR by KNN:

The `KNNClassifier.cs` file contains a robust implementation of the K-Nearest Neighbors (KNN) algorithm specifically designed for predicting input values from Sparse Distributed Representations (SDRs). This implementation is optimized for high-dimensional data processing commonly found in SDR-based systems and integrates core machine learning principles to ensure both versatility and efficiency in real-world applications.

**1. Data Structures and Initialization**
- **`DefaultDictionary<TKey, TValue>`**: custom dictionary implementation that automatically provides default values when a key is missing. This simplifies handling new input sequences without requiring predefined classifications, ensuring smoother operation.
- **Model Storage**: The classifier leverages a dictionary to associate classification labels with SDR sequences. Each label maps to a list of integer arrays, where each array represents an SDR sequence tied to that specific label.

**2. Learning Phase**
- **`Learn(TIN input, Cell[] cells)`**: This method processes the input values and their associated SDR cell indices. These values are then stored in the `models` dictionary under the appropriate classification label.
- **`UpdateModels(string classification, int[] cellIndices)`**: This method updates the SDR sequences within the model for a specific classification. It avoids duplication by checking if a sequence already exists before adding it. If the number of sequences exceeds a specified limit (`sdrs`), the oldest sequence is removed to maintain model efficiency.
- **Sequence Management**: The classifier ensures that the number of SDR sequences for each classification is kept within manageable limits, optimizing both memory usage and prediction performance.

**3. Distance Calculation**
- **`LeastValue(int[] classifiedSequence, int unclassifiedIdx)`**: This method calculates the Hamming distance between the SDR sequences. It identifies the minimum distance between corresponding bits of two sequences, which is critical for determining the nearest neighbors.
- **`ComputeCosineSimilarity(HashSet<int> classifiedSet, HashSet<int> unclassifiedSet)`**: This method calculates the cosine similarity between two sets of indices, representing the classified and unclassified sequences. The similarity score is then converted into a distance metric for use in KNN classification.
- **`GetDistanceTable(int[] classifiedSequence, int[] unclassifiedSequence)`**: This method generates a distance table that maps unclassified indices to the shortest distances from classified sequences, using the `LeastValue` method.

**4. Prediction Approaches**
- **`GetPredictedInputValues(Cell[] unclassifiedCells, short howMany = 1)`**: This method predicts the next input values based on the unclassified SDRs. It supports two distinct prediction approaches:
  - **Simple Weightage Approach**: This approach uses the Hamming distance to calculate similarity scores and weighted votes for each classification. The classification with the highest cumulative weight is selected as the prediction.
  - **Softmax Approach**: For a more probabilistic prediction, this method applies a Softmax function to the calculated distances. The method `PredictWithSoftmax(Cell[] unclassifiedCells, short howMany = 1)` is responsible for this approach, where Softmax normalization of distance metrics results in probability scores. The classification with the highest probability is selected as the prediction.
- **`SelectBestClassification(Dictionary<int, List<ClassificationAndDistance>> mapping, int howMany, int numberOfNeighbors)`**: This method aggregates the votes from the nearest neighbors, normalizes them, and selects the best classification based on the highest similarity score. It is used to finalize the prediction from the calculated distances.


#### Example Scenario:

Sample data:

```python
models = {
    "Category X" : [[5, 8, 12, 15, 19, 21, 24], [7, 9, 11, 14, 16, 20, 23]],
    "Category Y" : [[3, 6, 9, 13, 18, 22, 25], [4, 7, 10, 12, 15, 19, 21]],
    "Category Z" : [[2, 4, 7, 11, 14, 17, 20], [1, 3, 8, 10, 13, 17, 22]]
}
unclassified = [5, 8, 12, 15, 19, 21, 24]
```
Verdict: "Category X" being the closest match. "Category Y" as the next closest match, and so on...

**PredictedInputValues** = [Category X,Category Y,....]

The output is a list of ClassifierResult objects that provide a verdict on the label prediction.

### Output Parameters:
Upon completion of the experiment, an `experiment_{experimentId}_{berlinTime:yyyyMMddHHmmss}_output.txt` file is generated in the "outputfile" container. Moreover, to keep a record of all executed experiments and the application's performance, the experiment run log is maintained in the "outputtable." The "UploadExperimentResult" class is implemented to save the data in the table. Below is a code snippet for reference:

```csharp
        public async Task UploadExperimentResult(IExperimentResult result)
        {
            try
            {
                // Initialize the TableServiceClient and TableClient
                TableServiceClient tableServiceClient = new TableServiceClient(this._config.StorageConnectionString);
                TableClient tableClient = tableServiceClient.GetTableClient(tableName: this._config.ResultTable);
                await tableClient.CreateIfNotExistsAsync();

                // Generate a unique RowKey
                string uniqueRowKey = Guid.NewGuid().ToString();

                // Create a table entity from the result
                var entity = new TableEntity(this._config.ResultTable, uniqueRowKey)
                {
                    { "ExperimentId", result.ExperimentId },
                    { "Name", result.Name },
                    { "Description", result.Description },
                    { "StartTimeUtc", result.StartTimeUtc },
                    { "EndTimeUtc", result.EndTimeUtc },
                    { "DurationSec", result.DurationSec },
                    { "InputFileUrl", result.InputFileUrl },
                    { "OutputFileUrl", result.OutputFileUrl },
                    { "Accuracy", result.Accuracy }
                };

                // Add the entity to the Azure Table
                await tableClient.AddEntityAsync(entity);
            }
            catch (Exception ex)
            {
                _logger?.LogError(ex, "Failed to upload to Table Storage");
                Console.Error.WriteLine($"Failed to upload to Table Storage: {ex.Message}");
            }
        }
```

## Azure Deployment Details

This section describes all the procedures and steps for deploying the project on Azure cloud.

**A. Create all the configurations in Azure i.e Resource Group, Storage Container , Queue, Table, Container Registry, Container Instances. Once the configuration is completed, all the configured details can be observed under resource group as shown in the below reference Image.**
<details>
<summary>Click to see the configuration details</summary>

```Docker:

  Resource Group:  RG-Team-rookie_developer

  Azure Container Registry: knncloudprojectcr

  PROD Azure Container Instances: knncloudprojectci

  Azure Storage: knncloudprojectstr

  Containers :  1. inputfile 2. outputfile
 
  Queue: knnqueue

  Table: outputtable

  ConnectionString: "",
 
```
</details>

<p align="center">
  <img src="fig4_resourceGroup.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 4: Azure Resource Group</em>
</p>

**B. Organize the Project for dockerization and generate a docker image**

1. In Visual Studio, Right Click on the `dockerfile` and Click `Build Docker Image`

<p align="center">
  <img src="docker desktop.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 5: Docker Desktop</em>
</p>

2. Use the following command to push the Docker image to Docker Hub:

```bash
docker push indranil96/mycloudproject:v1
```

<p align="center">
  <img src="docker hub.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 6: Docker Hub</em>
</p>

3. Tag and Push Docker Image to Azure Container Registry:

```bash
docker tag mycloudproject:dev knncloudprojectcr.azurecr.io/mycloudproject:v2
docker push knncloudprojectcr.azurecr.io/mycloudproject:v2

```

<p align="center">
  <img src="fig7_containerRegistry.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 7: Azure Container Registry</em>
</p>

**C. Start the Instance and Pull the already created Docker Image:**
  1. Instance Start-Up: Start an Azure Container Instance in the Azure Resource Group.

<p align="center">
  <img src="fig8_containerInstance.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 8: Azure Container Instance</em>
</p>

  2. Corresponding Container Instance events and logs are displayed as shown below -

<p align="center">
  <img src="fig9_containerInstanceEvents.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 9: Azure Container Instance Events</em>
</p>

<p align="center">
  <img src="fig10_containerInstanceLogs.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 10: Azure Container Instance logs</em>
</p>


## Experiment Result Analysis

To facilitate multiple experimental setups, a variety of input files were stored in the inputfile blob storage container. Each file, in .txt format, includes both training (Train) and testing (Test) datasets. These files were designed with different combinations of training and testing data, varying sequence sizes, and datasets to enable a comprehensive evaluation. A total of 18 experiments were conducted, utilizing unique configurations of train-test data to explore the impact of sequence size and dataset composition on performance. Refer to Figure 10 for a visual representation of the data structure.

### Input Files given to the blob storage:

For multiple experiments of the project, several input files with different combinations of train and test data have been utilized, containing various sequence sizes and datasets in the inputfile blob storage container as shown in Figure 11. Each input file, in `.txt` format, contains both "Train" data and "Test" data. More than twenty experiments have been conducted using these input files, each featuring unique combinations of train and test data with varying sequence sizes and datasets.

   - [Sample Input File 1](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp1.txt)
   - [Sample Input File 2](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp2.txt)
   - [Sample Input File 3](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp3.txt)
   - [Sample Input File 4](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp4.txt)
   - [Sample Input File 5](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp5.txt)
   - [Sample Input File 6](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp6.txt)
   - [Sample Input File 7](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp7.txt)
   - [Sample Input File 8](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp8.txt)
   - [Sample Input File 9](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp9.txt)
   - [Sample Input File 10](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp10.txt)
   - [Sample Input File 11](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp11.txt)
   - [Sample Input File 12](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp12.txt)
   - [Sample Input File 13](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/InputFiles/Train_Test_Sequnces_exp13.txt)

<p align="center">
  <img src="fig11_inputfileBlob.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 11: Azure Input Blob Storage</em>
</p>

### Message Queues:

In the context of classification, different methods and metrics can be used depending on whether the SoftMax algorithm is employed. Below are two approaches outlining the processes and methods used for prediction, classification, and metrics evaluation.

1. **With Softmax Enabled** (`useSoftmax: true`):


**Prediction Method:** The system utilizes the SoftMax algorithm.

**Process:**
- **Prediction:** The `PredictWithSoftmax` method generates the initial predictions.
- **Weight Calculation:** The `CalculateSoftmaxWeights` method calculates the weights associated with the predictions.

**Metrics Evaluation:**
- The `ComputeCosineSimilarity` method assesses vector similarity.
- The `GetDistanceTableforCosine` method creates a distance table based on cosine similarity.

**Final Classification:**
- The Softmax method computes the probabilities, refining the classification output based on the SoftMax algorithm.

A queue message with useSoftmax `True` is defined as follows:

   ```json
   {
      "ExperimentId": "3",
      "Name": "Experiment 2a",
      "Description": "Running KNN Classifier with 15 train & 5 test data",
      "InputFile": "Train_Test_Sequnces_exp2.txt",
      "useSoftmax": true
   }
```

<p align="center">
  <img src="queue message true.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 12: Message Queue with useSoftmax: true</em>
</p>

2. **With Softmax Disabled** (`useSoftmax: false`):

**Prediction Method:** The system uses a Simple Weightage Algorithm.

**Process:**
1. **Prediction:** The `GetPredictedInputValues` method generates the initial predictions.
2. **Classification:** The `SelectBestClassification` method determines the best classification based on the predictions.

**Metrics Evaluation:**
- The system may use the `LeastValue` method for minimal distance calculation.
- The `ComputeCosineSimilarity` method can also be used to measure similarity.

**Distance Table:**
- The `GetDistanceTable` method creates a general distance table.
- If cosine similarity is required, `GetDistanceTableforCosine` is used.

  A queue message with useSoftmax `False` is defined as follows:
  
   ```json
   {
      "ExperimentId": "4",
      "Name": "Experiment 2b",
      "Description": "Running KNN Classifier with 15 train & 5 test data",
      "InputFile": "Train_Test_Sequnces_exp2.txt",
      "useSoftmax": false
   }
   ```

<p align="center">
  <img src="queue message false.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 13: Message Queue with useSoftmax: false</em>
</p>

### Output files generated from the blob storage:

The output files generated from the experiments are stored in Azure Blob Storage for persistent, organized, and scalable access. In the blob storage, we store the results in three separate folders:

**OCR Extracted Text** – This folder contains .txt files with the extracted text from all the preprocessed techniques.
**Preprocessed Images** – This folder contains the output images from all preprocessing techniques applied to the input.
**Result Similarity Matrix** – This folder contains the similarity values among all the preprocessing techniques.

These files contain the results of the experiments, which can be used for further analysis.

<p align="center">
  <img src="fig14_outputfileBlob.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 14: Azure Output Blob Storage </em>
</p>
**Sample Result of an Output Console Log -- to be added**

### Output Tables generated upon successful run:

Upon successful completion of the experiment, the application determines the best preprocessing technique for extracting text from the given input image.
In addition, to track all preprocessing techniques applied to the input image and the number of characters extracted by each technique, the application updates the outputTable.
This table stores the results for every preprocessing variation, including the extracted text length, the best-performing method, and its corresponding factor value.

- PartitionKey – Experiment name, used to group related records.
- RowKey – A unique identifier for the record (GUID).
- ExperimentId – Unique ID of the experiment.
- InputImageName – Name of the input image file used in the experiment.
- Best_Preprocessing_Technique – The best-performing preprocessing technique for the given input image.
- Factor – Scaling factor or related numeric parameter for preprocessing.
- Timestamp – UTC date/time when the record was inserted.
- ETag – Used for concurrency control in Azure Table Storage.
- Language – Language associated with the input image.
- Duration – Total execution time of the experiment.
- Bilateral_Filter_with_Brightness	Extracted character length for bilateral filter + brightness adjustment.
- Bilateral_Filter_with_Contrast	Extracted character length for bilateral filter + contrast adjustment.
- Bilateral_Filter_with_Rotation	Extracted character length for bilateral filter + rotation.
- Bilateral_Filter_with_Saturation	Extracted character length for bilateral filter + saturation adjustment.
- Gaussian_Filter_with_Brightness	Extracted character length for Gaussian filter + brightness adjustment.
- Gaussian_Filter_with_Contrast	Extracted character length for Gaussian filter + contrast adjustment.
- Gaussian_Filter_with_Rotation	Extracted character length for Gaussian filter + rotation.
- Gaussian_Filter_with_Saturation	Extracted character length for Gaussian filter + saturation adjustment.
- Gray_Scale_conversition_with_Brightness	Extracted character length for grayscale conversion + brightness adjustment.
- Gray_Scale_conversition_with_Contrast	Extracted character length for grayscale conversion + contrast adjustment.
- Gray_Scale_conversition_with_Rotation	Extracted character length for grayscale conversion + rotation.
- Gray_Scale_conversition_with_Saturation	Extracted character length for grayscale conversion + saturation adjustment.
- Median_Filter_with_Brightness	Extracted character length for median filter + brightness adjustment.
- Median_Filter_with_Contrast	Extracted character length for median filter + contrast adjustment.
- Median_Filter_with_Rotation	Extracted character length for median filter + rotation.
- Median_Filter_with_Saturation	Extracted character length for median filter + saturation adjustment.

<p align="center">
  <em>Figure 15: Azure Output Table </em>
</p>


#### Table 1: Image Processing Results
<table>
  <tr>
    <th>PartitionKey</th>
    <th>RowKey</th>
    <th>Best_Preprocessing_Technique</th>
    <th>Bilateral_Filter_with_Brightness</th>
    <th>Bilateral_Filter_with_Contrast</th>
    <th>Bilateral_Filter_with_Rotation</th>
    <th>Bilateral_Filter_with_Saturation</th>
    <th>Description</th>
    <th>Duration_in_Seconds</th>
    <th>ExperimentId</th>
    <th>Factor</th>
    <th>Gaussian_Filter_with_Brightness</th>
    <th>Gaussian_Filter_with_Contrast</th>
    <th>Gaussian_Filter_with_Rotation</th>
    <th>Gaussian_Filter_with_Saturation</th>
    <th>Gray_Scale_conversition_with_Brightness</th>
    <th>Gray_Scale_conversition_with_Contrast</th>
    <th>Gray_Scale_conversition_with_Rotation</th>
    <th>Gray_Scale_conversition_with_Saturation</th>
    <th>InputImageName</th>
    <th>Language</th>
    <th>Median_Filter_with_Brightness</th>
    <th>Median_Filter_with_Contrast</th>
    <th>Median_Filter_with_Rotation</th>
    <th>Median_Filter_with_Saturation</th>
    <th>OCRExtractedText</th>
    <th>PreProcessedImages</th>
    <th>ResultSimilarityMatrix</th>
  </tr>
  <tr>
    <td>TesseractCoders</td>
    <td>034ca7fb-39ca-498a-8c82-f5326ca8ab8a</td>
    <td>gaussian_with_contrast</td>
    <td>String</td>
    <td>67</td>
    <td>63</td>
    <td>1</td>
    <td>68</td>
    <td>OCR text extraction from all images in InputImages folder</td>
    <td>315</td>
    <td>exp-ocr-001</td>
    <td>1.3</td>
    <td>53</td>
    <td>36</td>
    <td>0</td>
    <td>41</td>
    <td>68</td>
    <td>36</td>
    <td>0</td>
    <td>41</td>
    <td>French_text</td>
    <td>French</td>
    <td>17</td>
    <td>15</td>
    <td>2</td>
    <td>23</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/OCRExtractedText/French_text</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/PreProcessedImages/French_text</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/ResultSimilarityMatrix/French_text</td>
  </tr>
  <tr>
    <td>TesseractCoders</td>
    <td>11d30f63-1e28-4e83-a3f6-a175340dc048</td>
    <td>gaussian_with_contrast</td>
    <td>45</td>
    <td>49</td>
    <td>4</td>
    <td>53</td>
    <td>OCR text extraction from all images in InputImages folder</td>
    <td>318</td>
    <td>exp-ocr-001</td>
    <td>0.9</td>
    <td>54</td>
    <td>41</td>
    <td>7</td>
    <td>54</td>
    <td>51</td>
    <td>41</td>
    <td>7</td>
    <td>54</td>
    <td>spanish_poem</td>
    <td>Spanish</td>
    <td>41</td>
    <td>28</td>
    <td>4</td>
    <td>44</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/OCRExtractedText/spanish_poem</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/PreProcessedImages/spanish_poem</td>
    <td>https://ourcloudprojectstorage.blob.core.windows.net/output/ResultSimilarityMatrix/spanish_poem</td>
  </tr>
</table>



**Proof of Experiments Conducted:**
1. [OutputFiles](https://studfrauasde-my.sharepoint.com/:f:/g/personal/riswan_saleem-basha2_stud_fra-uas_de/EmiPGStNXmlAqVLTPKtILycBWaNpZdu0HGl3gaecBiMGhw?e=szHAPB) --> Output Files containing each console logs has been pushed into OneDrive.
2. [OutputTable](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/tree/rookie_developers/Source/MyCloudProjectSample/OutputTable) --> Output Table is exported as .csv file from the Microsoft Storage Explorer Application

<p align="center">
  <img src="table result experiment.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 16: Table Result of Experiment Conducted</em>
</p>

<p align="center">
  <img src="result for 1000 data.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 17: Experiment 24 Visualization for handling 1000 values</em>
</p>

The above figure represents the accuracy over cycles 159 to 312 for Experiment 24, which involved processing a sequence with **1000 values**. Each bar corresponds to a specific cycle, with the height reflecting the accuracy percentage achieved during that cycle. This visualization serves as a detailed example of one of the experiments conducted in a broader series of experiments, illustrating the progression of accuracy within a single experimental run.

<p align="center">
  <img src="experiment_duration_histogram.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 18: Histogram representing experiment IDs with duration of run</em>
</p>

<p align="center">
  <img src="experiment_accuracy_linegraph.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 19: Line graph representing experiment IDs with accuracy achieved</em>
</p>

### Illustration of the result visualization:

These above visual representations of results summarize the results of various experiments, highlighting:

- **Duration (Sec):** Shown by the bars for both type of experiments without and with Softmax used.
- **Accuracy (%):** Illustrated by the orange line, showing the final test accuracy achieved in each experiment.

These charts provide a quick comparison of how the use of Softmax impacts experiment's duration and accuracy.

**Why is Accuracy Low in Some Experiments?**

- Softmax Computational Overhead: The Softmax function increases computational complexity. If the dataset or model architecture is not optimized for probabilistic interpretation, this can adversely affect accuracy.

- Data Variability Challenges: Experiments involving highly variable or complex data sequences may reduce the model's ability to make accurate predictions, particularly when the data is not representative of the target domain.

- Suboptimal Parameter Configuration: Inadequate hyperparameter settings, such as an improperly chosen number of neighbors in KNN or misconfigured "softness" parameters in Softmax, can negatively impact the model's performance.

**Potential Improvements**

- **Hyperparameter Optimization**: Experiment with different parameter values to identify the most effective configuration for improved model performance.
  
- **Enhanced Data Preprocessing**: Ensure data sequences are well-processed, balanced, and accurately represent the problem space to improve learning outcomes.
  
- **Cross-Validation Techniques**: Implement cross-validation methods to mitigate overfitting and enhance the model’s generalization capability across different datasets.
  
- **Misclassification Analysis**: Investigate misclassified instances to identify patterns of failure, refine the model, and address underlying weaknesses.

- **Exploring Alternative Approaches**: Assess other classifiers or incorporate regularization techniques to enhance model robustness and adaptability.  


**Note**

The current configuration imposes high computational demands, making it impractical to run thousands of experiments. Each sequence undergoes **3,500 cycles of training and testing**, generating extensive logs that significantly increase the output file size. With just **10 sequences, the output files can reach up to 200 MB**, and scaling this to **1,000 experiments could result in files exceeding 100 GB**. Additionally, the current setup requires approximately **two days to process 10 sequences**, meaning that running **1,000 experiments would take several months**.  

Given these constraints, alternative approaches should be considered to optimize performance and resource utilization. Possible solutions include:  

- **Optimizing the Logging Process**: Reducing unnecessary log entries to minimize output file size.  
- **Reducing the Number of Cycles**: Evaluating the impact of fewer training/testing cycles on model performance.  
- **Selecting a Representative Subset**: Running experiments on a strategically chosen subset rather than the entire dataset to achieve meaningful results with lower computational cost.  

Implementing these optimizations can improve efficiency while maintaining the integrity and effectiveness of the experimental process.  

### Result Inference: 

#### Overview  
This section outlines the results and implementation details of the KNN classifier experiment, focusing on practical insights to aid developers in replicating and extending the solution.  

#### Experiment Configurations  

##### Dataset Variations  
The experiment was executed using different combinations of training and test datasets:  
- **Single Large Dataset** – A single, large input dataset with long sequences was processed.  
- **Multiple Input Files** – Multiple smaller input files with varying sequences were processed in parallel.

#### Output Generation  
- The system generated multiple output files and tables, with results presented in a structured and organized format.  

#### Dynamic Algorithm Selection  

##### Key Implementation  
The KNN classifier dynamically switches between prediction algorithms based on the `useSoftmax` field in the queue message:  
- **SoftMax Algorithm** – Applied when `useSoftmax = true`.  
- **LeastValue Algorithm** – Applied when `useSoftmax = false`.  

This automation streamlines execution by eliminating the need for manual configuration, a limitation present in the original SE project.  

##### Previous Workflow (Manual)  
- In the original SE project, the softmax option had to be explicitly set (true or false) and manually executed for each run.  

##### Current Workflow (Automated)  
- A queue messaging system now automates algorithm selection, dynamically adjusting the classifier’s behavior based on incoming messages.  
- This enhancement increases efficiency and flexibility, reducing manual intervention. 

#### Scalability and Performance  

- **Large Datasets** – Experiments with higher volumes of training and test data demonstrated the system’s ability to scale effectively.  
- **Resource Utilization** – Processing large datasets required more computational resources and longer execution times, validating scalability.

#### Output Table Breakdown  

The output tables present results in a detailed format, highlighting key fields and corresponding values. This structured presentation simplifies validation and analysis for developers.  

#### Key Benefits  
- *Automation* – Reduces manual effort through dynamic algorithm selection.  
- *Scalability* – Handles large datasets efficiently.  
- *Cloud Integration* – Leverages cloud architecture for enhanced performance.  

#### Next Steps  
Developers can integrate the dynamic KNN classifier into their projects by replicating the queue messaging system and customizing the algorithm selection criteria as needed.


## Guide to Run the Project in Cloud

This section provides a detailed approach to execute the experiment.

**Step 1:** - Clone the GitHub Repo by the command, 

```
git clone https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024.git
```
**Step 2:** - Go to go the respective branch by the command,

```
git branch rookie_developers
```

**Step 3:** - Open Visual Studio, Go to MyCloudProjectSample -> Open the .sln file -> Build the Project.

**Step 4:** - In MyCloudProject, Right click on `Dockerfile` -> click `Build Docker Image`.

**Step 5:** - Once the build of Docker Image is successful, tag the image properly before pushing it to the DockerHub.

```
docker tag mycloudproject:dev indranil96/mycloudproject:v1
```

**Step 6:** - push the image with the right tag to the DockerHub

```
docker push indranil96/mycloudproject:v1
```

**Step 7:** - Once the push to hub is succesful, create the necessary Resource groups and respective services such as `Azure Container Registery` and `Azure Storage Account` and `Azure Container Instances` as mentioned in the above topic "Azure Deployment Details".

**Step 8:** - Tag the image properly for Azure Container Instance (ACR). provide the right local tag, container registry server, and the repository name and tag.

```
docker tag mycloudproject:dev knncloudprojectcr.azurecr.io/mycloudproject:v2
```

**Step 9:** - Log in to Azure Container Instance (ACR) Before Pushing.

```
az acr login --name knncloudprojectcr
```

**Step 10:** - push the image to Azure Container Instance (ACR),

```
docker push knncloudprojectcr.azurecr.io/mycloudproject:v2
```

**Step 11:** - `Add the necessary input files in the inputfile` storage blob for the experiment as shown in Figure 10 in the above topic "Experiment Result Analysis".

**Step 12:** - `Add the message queue in the storage queue` to run the experiment in the instance as shown in Figure 11 in the above topic "Experiment Result Analysis".

**Step 13:** - Once every configuration setup is done, `Run the Container Instance` as shown in Figure 7 in the above topic "Azure Deployment Details".

**Step 14:** - After the project execution is finished and you have collected the output data, it's recommended to stop the Azure Container Instance (ACI) to conserve Azure resources and ensure the system operates efficiently.

Upon Successful run, **outputfile** blob container and **outputtable** will be generated respectively.  

[Back to Table of Content](https://github.com/UniversityOfAppliedSciencesFrankfurt/se-cloud-2023-2024/blob/rookie_developers/Source/MyCloudProjectSample/Documentation/Investigate%20and%20Implement%20KNN%20classifier_ReadME.md#table-of-contents)

## Future Scope:
1. Enhanced feature selection and reduction in dimensionality: Techniques like Principal Component Analysis (PCA) and t-Distributed Stochastic Neighbor Embedding (t-SNE) can be applied to reduce the dimensionality of the dataset, addressing the "curse of dimensionality" and improving the performance of the KNN classifier.
   - PCA simplifies the dataset by transforming it into a smaller set of principal components, retaining most of the variance and discarding less significant features. This improves computational efficiency while maintaining essential data patterns.
   - t-SNE focuses on preserving local structures in the data, making it particularly useful for visualizing and understanding clusters, which enhances the accuracy of KNN's neighbor selection.
2. Scalability with Big Data Technologies: To handle excessively large datasets and enable real-time data processing, big data tools like Azure Synapse Analytics can be integrated into the workflow.
   - Azure Synapse Analytics offers a scalable platform that combines big data and data warehousing capabilities, making it well-suited for managing large-scale datasets. It supports parallel processing and distributed data storage, ensuring efficient handling of high-volume data.
   - Real-time data processing can be achieved by leveraging features such as Azure Data Explorer and streaming pipelines, enabling the system to process incoming data on-the-fly and update predictions or insights dynamically.
3. Cloud-Based Hyperparameter Optimization: Azure Machine Learning can be leveraged for automated hyperparameter tuning, significantly improving the accuracy and reliability of the KNN model. Key advantages include:
   - Scalable Optimization: Runs multiple experiments in parallel on Azure's distributed infrastructure, speeding up the tuning process.
   - Automated Search Techniques: Supports methods like grid search, random search, and Bayesian optimization to find the best hyperparameter configuration.
   - Real-Time Monitoring: Provides an interface to track experiment progress and metrics such as accuracy and precision.
   - Integration with Pipelines: Easily integrates into machine learning workflows, automating the tuning process alongside data preprocessing and model training.
   - Cost Efficiency: Dynamically allocates resources to minimize costs while ensuring optimal performance.
   - Enhanced Model Accuracy: Optimizes key KNN hyperparameters such as the number of neighbors (k), distance metrics, and weight functions to improve predictive performance.

## Conclusion

This project is designed with scalability and efficiency in mind, ensuring that it can handle increasingly large datasets without compromising performance or accuracy. Key strategies include continuous monitoring, systematic performance evaluation, and regular optimization to address emerging challenges while leveraging new opportunities. These practices ensure that the solution remains robust, adaptable, and capable of evolving in dynamic environments.

### Justification for KNN Classifier

A critical decision in this project was the selection of **K-Nearest Neighbors (KNN) as the final classifier**. The choice of KNN is justified due to the following reasons:

- **Simplicity and Effectiveness**: KNN is a non-parametric, instance-based learning algorithm that performs well in cases where data distributions are complex and non-linear.  
- **Handling Sparse Representations**: Since the HTM classifier outputs Sparse Distributed Representations (SDR), KNN can effectively measure similarity between these high-dimensional, sparse data points.  
- **Comparative Performance**: While the **HTM classifier** is useful for learning temporal patterns and anomaly detection, KNN was chosen for its **ability to handle classification tasks with clear decision boundaries**, making it suitable for the final stage of our process.  
- **Flexibility**: KNN does not require extensive training, making it easier to adapt and fine-tune based on new data.

A comparative evaluation was conducted to assess the suitability of KNN against alternatives like the **HTM classifier itself**. While the HTM classifier excels in detecting sequential dependencies, it was found that **KNN provided more reliable classification accuracy for discrete pattern recognition**, particularly in non-sequential datasets.

By integrating these methodologies and refining our approach, this project ensures a scalable and adaptable solution that can evolve with growing data needs and emerging challenges. The combination of **HTM’s temporal learning capabilities and KNN’s classification strengths** creates a well-balanced system optimized for both learning and decision-making. Regular performance evaluations and continuous improvements will further enhance its robustness and effectiveness in practical applications.

## References
1. [Neocortex API](https://github.com/ddobric/neocortexapi)
2. [KNN concepts](https://www.ibm.com/topics/knn)
3. [Microsoft Azure Deployment Guide](https://learn.microsoft.com/en-us/azure/app-service/deploy-best-practices)
