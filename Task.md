# Building Footprint Detection and Damage Assessment from Satellite Images

## Problem Statement

As a geospatial InsurTech firm, we manage extensive datasets of Satellite and Aerial Imagery. 
One significant challenge we encounter involves the identification of building footprints within numerous images and the subsequent evaluation of their structural integrity.

## Requirements

For this task, we require the development of a Computer Vision Model with the capability to perform the following:

- Import satellite or aerial images from the designated data directory.
- Conduct image preprocessing to optimize data quality.
- Detect major types of objects such as buildings, cars or airplanes.
- Employ detection techniques to identify the precise building footprints.
- Generate a GeoJSON file containing polygon representations of the detected building footprints.
- Create new images with the building footprints superimposed for visualization.
- Obtain or generate tagged images of damaged buildings.
- Determine the presence or absence of damage to the buildings.
- Assess the degree of damage, categorizing it on a scale ranging from 'none' to 'low,' 'medium,' 'high,' or 'full.'

## Submission

Please establish a public GIT repository and commit your code, dataset, and a comprehensive report detailing your observations and insights.

This is an open-ended test, allowing you to address as many or as few requirements as you see fit. If any requirements cannot be met, we request that you provide a detailed plan outlining the approach you would take to fulfill them.

## Assessment

Your evaluation will be based on the quality of your code, the accuracy of your results, the clarity of your report, and the correctness of the generated GeoJSON file.