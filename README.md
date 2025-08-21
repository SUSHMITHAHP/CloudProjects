# CloudProjects

<p align="center">
  <img src="readmeAssets/Input-files.JPG" alt="input">
</p>
<p align="center">
  <em>Figure 1: Azure Input Blob Storage</em>
</p>
<p align="center"><i>Fig.1 - inputfiles</i></p>

[Sample input](https://portal.azure.com/#view/Microsoft_Azure_Storage/ContainerMenuBlade/~/overview/storageAccountId/%2Fsubscriptions%2Fd60f2036-12f5-499d-af22-ef3afc698896%2FresourceGroups%2FRG-TesseractCoders%2Fproviders%2FMicrosoft.Storage%2FstorageAccounts%2Ftesseractcoders/path/input-files/etag/%220x8DDDA6E35FE4FA9%22/defaultId//publicAccessVal/Container)

[OutputFiles](https://portal.azure.com/#view/Microsoft_Azure_Storage/ContainerMenuBlade/~/overview/storageAccountId/%2Fsubscriptions%2Fd60f2036-12f5-499d-af22-ef3afc698896%2FresourceGroups%2FRG-TesseractCoders%2Fproviders%2FMicrosoft.Storage%2FstorageAccounts%2Ftesseractcoders/path/output-files/etag/%220x8DDDA6E3F015C87%22/defaultId//publicAccessVal/Container)

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

## Experiment Result Analysis

To support the experiment, all necessary input files were uploaded to Azure Blob Storage in a single container named input-files. This container directly holds different file types such as JPG, PNG, and ZIP files, ensuring that all resources are centralized and easily accessible. By keeping every input file in one well-maintained container, the experiment guarantees consistent data availability for reliable testing, comparison, and validation of results.

The input files includes 100 images covering all supported languages. Each image undergoes a series of preprocessing techniques to enhance text extraction. The most effective preprocessing method for each image is determined, and the extracted characters from all techniques are stored back in Azure Blob Storage. This approach ensures optimized OCR results and maintains a complete record of all processed data for further analysis.

### Input Files Given to the Blob Storage:

The input files used in the experiments are maintained in Azure Blob Storage within a single container named input-files. The resources include:

**Image Files** (JPG, PNG) – Text images in multiple languages (e.g., English, French, German) are stored directly in the container. This multilingual collection enables targeted OCR analysis and performance comparison across diverse datasets.

**ZIP Files** – Compressed datasets are also stored directly in the container, allowing bulk processing and efficient handling of larger file groups.

This centralized yet multilingual organization ensures streamlined workflows, reproducible results, and comprehensive evaluation of preprocessing techniques across different file formats and languages.

 - [Sample Input File](https://portal.azure.com/#view/Microsoft_Azure_Storage/ContainerMenuBlade/~/overview/storageAccountId/%2Fsubscriptions%2Fd60f2036-12f5-499d-af22-ef3afc698896%2FresourceGroups%2FRG-TesseractCoders%2Fproviders%2FMicrosoft.Storage%2FstorageAccounts%2Ftesseractcoders/path/input-files/etag/%220x8DDDA6E35FE4FA9%22/defaultId//publicAccessVal/Container)


<p align="center">
  <img src="readmeAssets/Input-files.JPG" alt="input">
</p>
<p align="center"><i>Fig.1 - Azure Input Blob Storage</i></p>

### Message Queues:

The Azure Queue message triggers the application and provides all necessary parameters for the experiment execution. The message is in JSON format and contains the following fields:

<table>
  <thead>
    <tr>
      <th>Field</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ExperimentId</td>
      <td>String</td>
      <td>A unique identifier for the experiment (e.g., exp-ocr-001).</td>
    </tr>
    <tr>
      <td>InputImage</td>
      <td>String</td>
      <td>The filename of the input image to be processed (e.g., oldText.png).</td>
    </tr>
    <tr>
      <td>OpenAIKey</td>
      <td>String</td>
      <td>The API key used for connecting to the OpenAI services.</td>
    </tr>
    <tr>
      <td>Name</td>
      <td>String</td>
      <td>The name of the experiment (e.g., ocr text extraction).</td>
    </tr>
    <tr>
      <td>Description</td>
      <td>String</td>
      <td>A short explanation of the experiment's purpose (e.g., OCR extraction from all images in the InputImages folder).</td>
    </tr>
    <tr>
      <td>Language</td>
      <td>String</td>
      <td>The language code for OCR processing (e.g., eng for English).</td>
    </tr>
    <tr>
      <td>ProcessingOptions</td>
      <td>Object</td>
      <td>
        Specifies processing parameters:<br>
        <ul>
          <li>BatchSize (Int) – Number of images processed per batch.</li>
          <li>ParallelProcessing (Bool) – Whether multiple images are processed in parallel.</li>
          <li>ProcessingMode (String) – The mode of operation, such as "Batch".</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>MessageId</td>
      <td>String</td>
      <td>The identifier of the queue message, used for tracking.</td>
    </tr>
    <tr>
      <td>MessageReceipt</td>
      <td>String/Null</td>
      <td>The receipt token for the queue message; can be null when first sent.</td>
    </tr>
  </tbody>
</table>

 A queue message defined as follows:
  
   ```json
   {
  "ExperimentId": "exp-ocr-001",
  "InputImage":"oldText.png",
  "OpenAIKey":" ",
  "Name": "ocr text extraction",
  "Description": "OCR text extraction from all images in InputImages folder",
  "Language": "eng",
  "ProcessingOptions": {
    "BatchSize": 10,
    "ParallelProcessing": true,
    "ProcessingMode": "Batch"
  },
  "MessageId": "testthismessage",
  "MessageReceipt": null
   }
   ```
<p align="center">
  <img src="readmeAssets/Message-queue.JPG" alt="Message-queue">
</p>
<p align="center"><i>Fig.2 - Message Queue</i></p>


### Output files generated from the blob storage:

The output files generated from the experiments are stored in Azure Blob Storage for persistent, organized, and scalable access. In the blob storage, the results are stored in three separate folders:

**OCR Extracted Text** – This folder contains .txt files with the extracted text from all the preprocessed techniques.

**Preprocessed Images** – This folder contains the output images from all preprocessing techniques applied to the input.

**Result Similarity Matrix** – This folder contains the similarity values among all the preprocessing techniques.

These files contain the results of the experiments, which can be used for further analysis.

<p align="center">
  <img src="readmeAssets/Output-files.png" alt="Output-files">
</p>
<p align="center"><i>Fig.3 -  Output Files</i></p>


**Sample Result of an Output Console Log**

<p align="center">
  <img src="readmeAssets/Console-log-1.png" alt="Consolelog">
</p>
<p align="center"><i>Fig.4 -  Console-log</i></p>

<p align="center">
  <img src="readmeAssets/Console-log-2.png" alt="Output-files">
</p>
<p align="center"><i>Fig.5 -  Console-log</i></p>

<p align="center">
  <em>Figure 13: Console-logs</em>
</p>


### Output Tables generated upon successful run:

Upon successful completion of the experiment, the application determines the best preprocessing technique for extracting text from the given input image.
In addition, to track all preprocessing techniques applied to the input image and the number of characters extracted by each technique, the application updates the outputTable.
This table stores the results for every preprocessing variation, including the extracted text length, the best-performing method, and its corresponding factor value.

- PartitionKey – Logical partition identifier in Azure Table Storage, used for load balancing and scalability.
- RowKey – Unique identifier within a partition; together with PartitionKey, ensures entity uniqueness.
- Timestamp – System-generated time at which the entity was last modified, used for concurrency control in Azure Table Storage.
- Best_Preprocessing_Technique – The preprocessing method that yielded the most accurate OCR result for the given input image.
- Bilateral_Filter_with_Brightness – Extracted character length after applying bilateral filter with brightness adjustment.
- Bilateral_Filter_with_Contrast – Extracted character length after applying bilateral filter with contrast adjustment.
- Bilateral_Filter_with_Rotation – Extracted character length after applying bilateral filter with image rotation.
- Bilateral_Filter_with_Saturation – Extracted character length after applying bilateral filter with saturation adjustment.
- Description – Metadata or descriptive details about the experiment run.
- Duration_in_Seconds – Total execution time of the experiment (in seconds).
- ExperimentId – Unique identifier assigned to each experiment for tracking and reproducibility.
- Factor – Parameter or scaling factor used in preprocessing or experiment configuration.
- Gaussian_Filter_with_Brightness – Extracted character length after applying Gaussian filter with brightness adjustment.
- Gaussian_Filter_with_Contrast – Extracted character length after applying Gaussian filter with contrast adjustment.
- Gaussian_Filter_with_Rotation – Extracted character length after applying Gaussian filter with image rotation.
- Gaussian_Filter_with_Saturation – Extracted character length after applying Gaussian filter with saturation adjustment.
- Gray_Scale_conversition_with_Brightness – Extracted character length after grayscale conversion with brightness adjustment.
- Gray_Scale_conversition_with_Contrast – Extracted character length after grayscale conversion with contrast adjustment.
- Gray_Scale_conversition_with_Rotation – Extracted character length after grayscale conversion with image rotation.
- Gray_Scale_conversition_with_Saturation – Extracted character length after grayscale conversion with saturation adjustment.
- InputImageName – The filename of the input image processed during the experiment.
- Language – The language associated with the text in the input image (e.g., English, French, German).
- Median_Filter_with_Brightness – Extracted character length after applying median filter with brightness adjustment.
- Median_Filter_with_Contrast – Extracted character length after applying median filter with contrast adjustment.
- Median_Filter_with_Rotation – Extracted character length after applying median filter with image rotation.
- Median_Filter_with_Saturation – Extracted character length after applying median filter with saturation adjustment.
- OCRExtractedText – The text recognized/extracted by OCR after preprocessing.
- PreProcessedImages – Storage reference or metadata for the preprocessed images used in the experiment.
- ResultSimilarityMatrix – Similarity comparison matrix of extracted text against reference text for accuracy evaluation.

<p align="center">
  <img src="readmeAssets/Result-table_1.png" alt="result table">
</p>
<p align="center"><i>Fig.5 -  result table</i></p>

<p align="center">
  <img src="readmeAssets/Result-table_2.png" alt="result table">
</p>
<p align="center"><i>Fig.5-  result table</i></p>

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


1. [OutputFiles]-(https://portal.azure.com/#view/Microsoft_Azure_Storage/ContainerMenuBlade/~/overview/storageAccountId/%2Fsubscriptions%2Fd60f2036-12f5-499d-af22-ef3afc698896%2FresourceGroups%2FRG-TesseractCoders%2Fproviders%2FMicrosoft.Storage%2FstorageAccounts%2Ftesseractcoders/path/output-files/etag/%220x8DDDA6E3F015C87%22/defaultId//publicAccessVal/Container).

<p align="center">
  <img src="table result experiment.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 16: Table Result of Experiment Conducted</em>
</p>

<p align="center">
  <img src="result for 100 data.png" alt="Your Image Alt Text" />
</p>
<p align="center">
  <em>Figure 17: Experiment  Visualization for handling 10 images</em>
</p>

**Experiment  Visualization for handling 10 images**

**Bar graph explaination**:
This bar chart illustrates the average extracted character length obtained from processing a bulk set of images through various preprocessing techniques.
Each bar represents a specific preprocessing method, such as Bilateral Filter, Gaussian Filter, Grayscale Conversion, or Median Filter, combined with different adjustments like Brightness, Contrast, Rotation, or Saturation.

The height of each bar reflects the effectiveness of that preprocessing technique in terms of the average number of characters extracted via OCR. Higher values indicate better text extraction performance for the corresponding method, while lower values suggest reduced text recognition output. This comparison helps in identifying which preprocessing combinations yield the most accurate and complete OCR results across a multilingual and varied dataset.

**Line graph**:

This line chart shows the execution duration (in seconds) for processing images associated with each experiment ID.

Each point on the graph corresponds to a specific experiment (e.g., EXP-OCR-001, EXP-OCR-002), with the y-axis indicating the total time taken to complete image processing in that experiment. Variations in duration reflect differences in factors such as image complexity, preprocessing techniques applied, and the number of images processed. Peaks indicate experiments requiring more processing time, while lower points correspond to faster execution.

**Average CPU Usage**
The graph shows a low, stable CPU usage of around 20% before the experiment began. At the start of the OCR process, CPU usage sharply increased to 80% and remained at that level. This indicates the OCR task is highly CPU-intensive, consuming most of the available processing power.

**Average Memory Usage**
The graph illustrates low memory consumption below 50MB before the OCR workload was initiated. When the experiment started, memory usage spiked to approximately 450MB and stayed constant. This shows that the OCR process requires a significant amount of RAM to load and process the images.
