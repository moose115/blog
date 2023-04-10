---
layout: single
title:  "Process data quickly with distributed Elixir"
header:
    image: /assets/images/blockchain.jpg
    teaser: /assets/images/blockchain.jpg
date:   2023-04-04 08:00:00 -0700
categories: languages elixir
toc: true
---
Computing has become increasingly critical as the use of data has exploded in recent years. Turning raw data into meaningful insights requires efficient data processing, which can be a resource-intensive task, particularly when dealing with massive data sets. Fortunately, there are solutions that can help streamline this process, and Elixir is one such tool. In this article, we will explore how Elixir's distributed capabilities can make data processing fast and efficient.

## Our use case

In this article we will look into open data from the [City of Calgary](https://data.calgary.ca/), specifically the [Building Permits](https://data.calgary.ca/Business-and-Economic-Activity/Building-Permits/c2es-76ed) dataset. This dataset contains information about building permits issued by the City of Calgary. It has over 414,000 rows and 30 columns, which make it a good candidate to show how distributing can speed up data processing. Our goal with this dataset is to create a new dataset that has insights about the permits in each quadrant of the city.

We will be using [GenStage](https://hexdocs.pm/gen_stage/GenStage.html), a behaviour for Elixir applications that is used for data processing pipelines. GenStage allows us to process data concurrently, thus improving processing speed. As the dataset is in CSV format, we will need [CSV](https://hexdocs.pm/csv/3.0.5/readme.html) to parse it. 

❗ **Note**: This article assumes you have a basic understanding of Elixir. To catch up on the topics covered in this article, read [Mix](https://elixirschool.com/en/lessons/basics/mix/), [OTP](https://elixirschool.com/en/lessons/advanced/otp-concurrency/) and [my previous article](https://mustaf115.github.io/blog/languages/elixir/2023/02/25/harnessing-elixir-for-fault-tolerant-distributed-systems.html) on distributed Elixir.
{: .notice--warning}

## The pipeline

Let's define the stages in our processing. We can break down the pipeline into three stages:
- **Producing**: This stage will read the CSV file and produce a stream of rows. In GenStage, this is called a producer.
- **Transforming**: This stage will transform the rows into a map that contains the quadrant and the number of permits in that quadrant. In GenStage, this is called a ProducerConsumer.
- **Consuming**: This stage will consume the map and write it to a CSV file. In GenStage, this is called a consumer.

