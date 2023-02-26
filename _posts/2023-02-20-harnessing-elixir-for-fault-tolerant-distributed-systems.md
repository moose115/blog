---
layout: single
title:  "Harnessing Elixir for Fault-Tolerant Distributed Systems"
header:
  image: /assets/images/elixir.jpg
  teaser: /assets/images/elixir.jpg
date:   2023-02-25 08:00:00 -0700
categories: languages elixir
toc: true
---
In today's world, technology has become an essential part of our daily lives. From the recent rise of AI text processors and image diffusers, to the ever-increasing demand for new streaming and real-time services, and even the hype around blockchain technology, it is clear that the world is constantly evolving and advancing through technology. However, there is one thing that ties all of these technologies together: they rely on a distributed architecture in order to function properly. In this article, we will explore the importance of distributed architecture in modern technology, and how it has become a key factor in the development of many of today's cutting-edge technologies.

## What is Distributed Architecture?

As technology advances, the need for immense processing power and availability has become increasingly crucial. However, relying on a single computer to handle these demands is no longer feasible. Instead of simply increasing the power of a single machine, distributed systems add more machines to the network, allowing applications to run across multiple devices and work together seamlessly.

The distribution of resources has allowed us to overcome the limitations of current technology and mitigate the costs associated with expensive hardware upgrades. This, in turn, allows for indefinite scaling and growth of the system.

While most programming languages do not come equipped with tools for distribution, some like Erlang have been designed with distribution and fault-tolerance in mind. Erlang, which shipped with the BEAM virtual machine and OTP framework over three decades ago, serves as the foundation for Elixir, a language that has been built on top of the robust distributed capabilities of Erlang to provide a modern, scalable solution for distributed computing.

## Enter Elixir

Elixir is a general-purpose programming language that is both functional and concurrent. It runs on the Erlang virtual machine, BEAM, and features a syntax heavily inspired by Ruby. Elixir's syntax makes it more accessible to developers familiar with mainstream languages, while also incorporating some of the features of Object Oriented languages as outlined by Alan Kay.

❗ **Note:** Elixir is *not* an object-oriented language. There are just [some similarities](https://qr.ae/priIjO).
{: .notice--warning}

Elixir builds on the foundation provided by Erlang's ecosystem while also providing its own standard library. The distribution capabilities of Elixir rely on its concurrency model, which is based on Erlang's Actor model. This model enables the creation of lightweight processes that can be used to run tasks concurrently. These processes are isolated from one another and communicate through message passing, without necessarily knowing whether they are running on the same machine or different machines.

One of the key features of Elixir, and Erlang, is the Open Telecom Protocol (OTP) which facilitates fault-tolerant systems. OTP allows processes to be organized into a supervision tree, where they are monitored and automatically restarted in the event of a failure. This ensures that the system continues to function even in the face of unexpected errors.

🧪 **Elixir's syntax** is not covered in this article. I will try to keep examples simple and explain them. If you want to learn more about Elixir's syntax, I recommend [Elixir School](https://elixirschool.com/en/) or official [Guides](https://elixir-lang.org/getting-started/introduction.html).
{: .notice--success}

## Briefly on Functional Programming

Functional programming is a programming paradigm that treats data in its raw form. While OOP tries to interpret data as object, entities whose attributes and behaviours that can be described and focused on, functional programming emphasises on **what** is happening to data. We do not focus on what data in a form of an object can do, what its methods can do, but rather what happens to data after it is passed through a function.

Elixir follows this paradigm and provides a clear and easy to follow syntax to represent flow of data through functions. The following snippets shows Elixir's approach compared to OOP's approach:

{% highlight elixir %}
# Elixir
def manipulate_list() do
    list = [1, 2, 3, 4, 5]
    list
    |> Enum.map(fn x -> x * 2 end)
    |> Enum.filter(fn x -> x > 5 end)
    |> Emum.sum()
    |> IO.inspect() # 24
end
{% endhighlight %}

Or if we expand the syntax:

{% highlight elixir %}
# Elixir
def manipulate_list() do
    list = [1, 2, 3, 4, 5]
    list1 = Enum.map(list, fn x -> x * 2 end)
    list2 = Enum.filter(list1, fn x -> x > 5 end)
    sum = Enum.sum(list2)
    IO.inspect(sum) # 24
end
{% endhighlight %}

In Java:

{% highlight java %}
// Java
public static void manipulateList() {
    int[] list = {1, 2, 3, 4, 5};
    
    for (int i = 0; i < list.length; i++) {
        list[i] = list[i] * 2;
    }

    for (int i = 0; i < list.length; i++) {
        if (list[i] <= 5) {
            list[i] = 0;
        }
    }

    int sum = 0;

    for (int i = 0; i < list.length; i++) {
        sum += list[i];
    }

    System.out.println(sum);

    for (int i = 0; i < list.length; i++) {
        System.out.print(list[i] + " "); // 0 0 6 8 10
    }
}
{% endhighlight %}

As we can see, Elixir treats the list as an input to a function and returns a new list as an output. Meanwhile, Java treats the array as an object and operates on it directly. Additionally, Elixir's syntax is more concise and easier to read.

💡 **Worry not!** Java can perform immutable operations on objects as well. One can use libraries like Apache's [Commons Lang](https://commons.apache.org/proper/commons-lang/) with its `ArrayUtils` class to conveniently operate on arrays.
{: .notice--info}

That flow of data in Elixir comes important when we talk about concurrency and message passing between processes. It helps OTP minimize data race conditions and deadlocks.

## Features of OTP

Now, having a basic understanding of Elixir and its functional programming paradigm, let's take a look at the features of OTP.

### Processes

Processes are the main building blocks of Elixir applications. They are lightweight, isolated, and can be used to run tasks concurrently. Processes are also the main way of communication between different parts of the system. They communicate with each other by sending and receiving messages rather than directly calling each other's functions. Main types of processes are:
- **GenServer:** Generic Server used as a general purpose process. May do processing and hold state.
- **Supervisor:** A process that monitors other processes and restarts them if they crash.
- **Agent:** A process that used specifically for holding a state and allows other processes to update it.
- **Task:** A process that runs a function asynchronously (concurrently).

They are the main backbone of Elixir applications. They are somewhat comparable to objects in OOP in their role in the system. However, they are not exactly processes as in an operating system. From the [Elixir's page](https://elixir-lang.org/getting-started/processes.html):
> Elixir’s processes should not be confused with operating system processes. Processes in Elixir are extremely lightweight in terms of memory and CPU (even compared to threads as used in many other programming languages). Because of this, it is not uncommon to have tens or even hundreds of thousands of processes running simultaneously.

### Supervision Trees

Supervision trees play a crucial role in making Elixir apps fault-tolerant. They are a tree-like structure that allows processes to be organized into a hierarchy, where each node is a supervisor and each leaf is a worker. The supervisor monitors the worker and restarts it if it crashes. This ensures that the system continues to function even in the face of unexpected errors.

| ![Supervision Tree](/blog/assets/images/supervision-tree-example.png) |
|:--:|
| *From [Isaac Jarquin's blog post](http://isaacjarquin.github.io/software/2017/09/01/supervision-tree.html). Each supervisor node is responsible for supervisors or worker leafs underneath.* |

### Message Passing

Processes communicate with each other by passing messages. When a process receives a message it is put into its mailbox, similar to buffered channels in Go's goroutines. The process can then retrieve the message from its mailbox and handle it. The following snipped shows how a process can send and receive messages:

{% highlight elixir %}
# Assign a receiver function to a variable
fun = fn -> 
    receive do
        {:hello, name} -> IO.puts "Hello #{name}"
        {:goodbye, name} -> IO.puts "Goodbye #{name}"
        _ -> IO.puts "I don't understand" # _ match any message
    end
end

# Spawn a process that will be receiving messages
pid = spawn(fun) # pid is the process identifier

# Send a message to the process
send(pid, {:hello, "John"})
# Hello John
{% endhighlight %}

In the above example, we have a process that receives messages and prints them out. We then spawn a process and send it a message. The process receives the message and prints it out.

However, this basic example is only unidirectional and not commonly used in Elixir. Instead, we use GenServers, which provide a load of abstractions over process creation and bidirectional message passing.

## Distributing Elixir Applications

A neat thing about processes is that we can name them. This way we can easily refer to them by their name instead of a mysterious PID. Using [Interactive Elixir (IEx)](https://hexdocs.pm/iex/IEx.html), we can run Elixir code in the terminal. Starting an IEx session itself creates a process that executes the code and can spawn other processes. Because it is a process, we can name it. Let's start two sessions and name them `sender` and `receiver`:

{% highlight bash %}
$ iex --sname sender
iex(sender@mustafa)1>
{% endhighlight %}

And:

{% highlight bash %}
$ iex --sname receiver
iex(receiver@mustafa)1>
{% endhighlight %}

💡 **Notice** there are `sender@mustafa` and `receiver@mustafa` in the prompt. The first part is, as you can guess, the name of the process. The second part is the hostname of the machine. In my case it is "mustafa", but it could be "DESKTOP-XXXXXXX" or anything you name it. OTP resolves the hostname to connect to other machines on the same network. This way all network communication is provided out of the box, making Elixir distributed applications easy to set up.
{: .notice--info}

❗ **Windows.** If you are using Windows Powershell, you need to use `iex.bat` instead of `iex`. Also, passing `--werl` will launch Erlang's shell which better supports Unicode characters and auto-completion.
{: .notice--warning}

First, let's make out sender create a process in the receiver and send it a message:

{% highlight elixir %}
iex(sender@mustafa)1> receiver = Node.spawn_link(:"receiver@mustafa", fn -> receive do
...(sender@mustafa)1>     {:hello, sender} -> send(sender, {:hello, "Hello from receiver"})
...(sender@mustafa)1>     _ -> IO.puts "I don't understand"
...(sender@mustafa)1> end end)
#PID<0.108.0>
{% endhighlight %}

By using Node.spawn_link, we communicate to another running process and make it execute the given function. All the function does is make the process wait for a message and print it out. Now, from the sender again, let's send a message to the receiver:

{% highlight elixir %}
iex(sender@mustafa)2> send(receiver, {:hello, self()})
{:hello, #PID<0.107.0>}
iex(sender@mustafa)3> flush()
{:hello, "Hello from receiver"}
:ok
{% endhighlight %}

Now that the receiver is listening for messages, we sent it one - a tuple with the atom `:hello` and the PID of the sender. The receiver then sends a message back to the sender. We can see that the message is received by the sender by using `flush()`. This function prints out all the messages in the mailbox of the current process. The `:ok` at the end is the return value of `flush()`.

Of course, in real world applications, we would use OTP's GenServers and all processes alike to build and connect processes together. But this is a good example of how Elixir makes it easy to write code that can be distributed across multiple machines.

## Caveats

This method of distribution is great, but has some flaws. Elixir and OTP provide an application level distribution, which is amazing at handling errors. However, it is developers' responsibility to make the processes aware of each other. Additionally, in case of a hardware failure, OTP will not be able to automatically restart a failed process, since the process does not even have a functioning environment to run on.

One solution to this is to use a container orchestration tool like Kubernetes. Although there is some overlap between K8s and OPT in distributing capabilities, they serve different, but complementary purposes. With K8s, we can containerize out application and run it on multiple machines, however this process is automated by K8s. Additionally, libraries like `libcluster` allow for automatic process discovery within a cluster further improving the developers' experience and speed.

## Conclusion

In conclusion, Elixir is a highly versatile language that runs on top of the Erlang ecosystem. Its syntax is designed to make functional programming more accessible, and its rich standard library provides developers with a broad range of tools for building distributed systems. Elixir's fault-tolerance features and ability to run on multiple machines make it an ideal choice for real-time services, as demonstrated by its adoption by major companies such as WhatsApp, Pinterest, and Discord.

Personally, I would love to see it used more in North American, and mainly Calgary's, startup scene.

## Further Reading

* [Elixir School](https://elixirschool.com/en/) - An online resource for learning Elixir
* [Elixir in Action](https://www.manning.com/books/elixir-in-action) - A tutorial book for learning Elixir, great for those coming from OO languages
* [Kuernetes and the Erlang VM: orchestration on the large and the small](https://blog.plataformatec.com.br/2019/10/kubernetes-and-the-erlang-vm-orchestration-on-the-large-and-the-small/), by José Valim - An article from the creator of Elixir on how Kubernetes and Elixir can work together