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

We will benchmark the performance of data processing in Elixir by generating a shuffled list of 1 million numbers and sorting them multiple times. We will use the built-in `Enum.sort/1` function to ensure that the sorting is done with the same time complexity. We will compare the performance of the following approaches:

- **Naive**: This is the simplest approach. We will sort the list in a single process, iterating over the list sequentially.
- **Concurrent**: This approach will leverage Elixir's concurrency capabilities. We will sort the list in multiple processes, iterating over the list concurrently.

I chose sorting a list of numbers as an example due to its technical simplicity. In the real world, the data could come from various sources such as a CSV file, database, message brokers like RabbitMQ, or event streams from Apache Kafka. The same principles apply to all of these use cases - concurrency can be leveraged to speed up the processing.

❗ **Note**: This article assumes you have a basic understanding of Elixir. To catch up on the topics covered in this article, read [Mix](https://elixirschool.com/en/lessons/basics/mix/), [OTP](https://elixirschool.com/en/lessons/advanced/otp-concurrency/) and [my previous article](https://mustaf115.github.io/blog/languages/elixir/2023/02/25/harnessing-elixir-for-fault-tolerant-distributed-systems.html) on distributed Elixir.
{: .notice--warning}

## Tools

The naive approach will only require functions from the `Enum` module. Although robust and powerful, these functions only allow for sequential processing, one element after another.

The concurrent approach will require the `Flow` library, which is built on top of the `GenStage` library, and relies on `GenServer` from the OTP (Open Telecom Platform) framework. The `Flow` library enables concurrent processing of data, which is a key feature of the concurrent approach. Additionally, it provides a simple interface, similar to the built-in `Enum` module.

## Benchmark setup

Let's start by setting up the Mix project. We will use the `mix new` command to create a new project named `elixir_bench`. For the sake of simplicity, we will skip the supervision tree.

```bash
mix new elixir_bench
```

This will create a new directory with a basic project boilerplate. Now, let's open the `mix.exs` file and add the `flow` dependency to the `deps` function.

```elixir
defp deps do
  [
    {:flow, "~> 1.2"}
  ]
end
```

And then run `mix deps.get` to fetch the dependency.

Let's also create new files under the `lib` directory. We will create a `naive.ex` and a `concurrent.ex` file, where we will put the code for the naive and concurrent approaches, respectively.

## Common code

We will start by creating a function that will generate a shuffled list of 1 million numbers and perform time measurement.

```elixir
def process_and_measure do
    list = 1..1_000_000 |> Enum.to_list() |> Enum.shuffle()
    
    start_time = Time.utc_now()
    
    # We will process here
    # ====================
    
    # ====================
    
    end_time = Time.utc_now()
    
    time_taken = Time.diff(end_time, start_time, :millisecond)
    IO.puts("Time taken: #{time_taken}ms")
end
```

We create a list from a range of 1 to 1 million. Then we shuffle the list using the `Enum.shuffle/1` function. We will use the `Time` module to measure the time it takes to process the list.

## Naive approach

The naive approach is really simple. We will just call the `Enum.sort/1` function on the list 20 times.

```elixir
1..20
    |> Enum.each(fn _ ->
        Enum.sort(list)
    end)
```

All it does is call the `Enum.sort/1` function 20 times. Let's put it all together in the `naive.ex` file.

```elixir
defmodule Naive do
    def process_and_measure do
        list = 1..1_000_000 |> Enum.to_list() |> Enum.shuffle()
        
        start_time = Time.utc_now()
        
        1..20
            |> Enum.each(fn _ ->
                Enum.sort(list)
            end)
        
        end_time = Time.utc_now()
        
        time_taken = Time.diff(end_time, start_time, :millisecond)
        IO.puts("Time taken: #{time_taken}ms")
    end
end
```

## Concurrent approach

The concurrent approach is more complex under the hood, but Flow library allows us to achieve the desired concurrency with one simple change.

```elixir
1..20
    |> Flow.from_enumerable(max_demand: 1)
    |> Flow.map(fn _ ->
        Enum.sort(list)
    end)
    |> Flow.run()
```

All we did was add `Flow.from_enumerable()` and replace the `Enum.each` function with `Flow.map` in the `concurrent.ex` file. The `Flow.from_enumerable()` function wraps the input data to be used by Flow, and the `:max_demand` parameter tells Flow to work with batches of size 1 (by default it is 500, which shows the scale at which it is normally used). The `Flow.map` function applies a function to each element in the flow, allowing for concurrent processing. Let's put it all together in the `concurrent.ex` file.

```elixir
defmodule Concurrent do
    def process_and_measure do
        list = 1..1_000_000 |> Enum.to_list() |> Enum.shuffle()
        
        start_time = Time.utc_now()
        
        1..20
            |> Flow.from_enumerable()
            |> Flow.map(fn _ ->
                Enum.sort(list)
            end)
            
        
        end_time = Time.utc_now()
        
        time_taken = Time.diff(end_time, start_time, :millisecond)
        IO.puts("Time taken: #{time_taken}ms")
    end
end
```

## Time to benchmark

With the code set up, let's run the `process_and_measure` function from both modules.

Let's enter IEx with `iex -S mix compile` and run the following commands. `-S mix compile` will compile the project and start an IEx session with the modules loaded.

```elixir
iex(1)> Naive.process_and_measure()
Time taken: 7263ms
:ok
iex(2)> Concurrent.process_and_measure()
Time taken: 3034
:ok
```

As we can see, by spreading as little as 20 tasks over concurrent processes, we were able to reduce the time taken by 58%. This is a significant improvement, and it will only get better as we increase the number of tasks. Let's increase the number of tasks to 100 (replacing `1..20` with `1..100`) and run the benchmark again.

```elixir
iex(1)> Naive.process_and_measure()
Time taken: 34791ms
:ok
iex(2)> Concurrent.process_and_measure()
Time taken: 12487ms
:ok
```

Now the time savings are up to 64%, and that's with only 100 tasks. As we increase the number of tasks, stages, and processing required, the savings will be even greater.

## Distribution and scalability

The presented approach, although currently on a single machine, can be scaled across multiple machines. Let's consider an efficient data source that can keep up with our processing power, such as Apache Kafka, which can stream hundreds of thousands of events per second, or a similar service like AWS Kinesis/SQS. We can define this data source in our Elixir application and run it in a distributed environment, such as a Kubernetes cluster or multiple machines, enabling almost unlimited scalability.

Elixir also offers other libraries, with one alternative to `Flow` being `Broadway`, which is tailored for working with sources like Amazon SQS, Apache Kafka, Google Cloud PubSub, or RabbitMQ. Even the bare `GenStage` library can be used to create custom tools for specific jobs.

## Conclusion

In this article we have seen how we can use Elixir's concurrency to achieve a significant performance boost. We have seen how we can use the Flow library to achieve this. We have also seen how we can scale this approach to achieve even greater performance.

I find functional programming in Elixir to be a pleasant and efficient way to write code, especially for data processing tasks. I hope you enjoyed this article and found it informative.