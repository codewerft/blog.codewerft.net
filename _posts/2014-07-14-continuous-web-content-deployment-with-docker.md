---
layout: post
title: Continuous Web Content Deployment with Docker and Git
---

## Scenario

You keep the sources of a website or blog project in a Git repository (e.g., on Github). One or more people are actively working on the project and updates need
to go 'live' frequently. You want to deploy the project automatically accross one or more web servers every time new changes are pushed to a specific branch of the repository. 

In addition, you want to be able to quickly add additional server instances to or remove existing server instances from the project to address fail-safety, scalability and cost-effective operation.

### Prerequisites

- You have installed [Docker](http://www.docker.com/) on one or more (web) servers.
- Your Git repository is either public or you have an OAuth access token in case it is private (GitHub access tokens are explained [here](https://help.github.com/articles/creating-an-access-token-for-command-line-use)).

---

## Deploying Static Web Content

Clone the [docker.webinstance](https://github.com/codewerft/docker.webinstance) repository and build the Docker container:

{% highlight Bash shell scripts%}
git clone https://github.com/codewerft/docker.webinstance.git
cd docker.webinstance

docker build --rm -t webinstance .
{% endhighlight %}

Launch a Docker `webinstance` container and point it to the Git repository where you store your code. The following options can be passed to the container:

- `WI_REPOSITORY` - The URL of the Git repository you want to deploy. 
- `WI_BRANCH` - The repository branch you want to deploy (default: master).
- `WI_OAUTH` - The OAuth token to access your repository in case it is private.
- `WI_POLL_INTERVAL` - Repository poll interval in minutes (default: 5).

{% highlight Bash shell scripts%}
docker run -p 127.0.0.1:5000:80 \
  --name=mywebsite -d webinstance \
  -e WI_REPOSITORY=https://github.com/user/mywebsite.git \
  -e WI_BRANCH=master \
  -e WI_OAUTH=secret
{% endhighlight %}

If you run the `docker ps` command, you should see your new `webinstance` container up and running.

{% highlight Bash shell scripts%}
b06bc6ab2cff        webinstance:latest      /usr/bin/supervisord   1 minute ago         Up 1 minute         127.0.0.1:5000->80/tcp   mywebsite       
{% endhighlight %}

In this example, port 80 of the container's Nginx server is exposed locally on port 5000, so you should be able to connect to it, e.g., via lynx:  
`lynx http://localhost:5000`.

---

## Deploying a Jekyll Blog

*Continuous deployment of a [Jekyll](http://jekyllrb.com/) blog works 
analogous to the deployment of a static website. The only difference is that 
 we use a `docker.jekyllinstance` container instead of a `docker.webinstance`.*

Clone the [docker.jekyllinstance](https://github.com/codewerft/docker.jekyllinstance) repository and build the Docker container:

{% highlight Bash shell scripts%}
git clone https://github.com/codewerft/docker.jekyllinstance.git
cd docker.jekyllinstance

docker build --rm -t jekyllinstance .
{% endhighlight %}

Launch a Docker `jekyllinstance` container and point it to the Git repository where you store your Jekyll blog:

{% highlight Bash shell scripts%}
docker run -p 127.0.0.1:6000:80 \
  --name=myblog -d jekyllinstance \
  -e WI_REPOSITORY==https://github.com/user/myblog.git \
  -e WI_BRANCH=master \
  -e WI_OAUTH=secret
{% endhighlight %}

In this example, port 80 of the container's Nginx server is exposed locally on port 6000, so you should be able to connect to it, e.g., via lynx:  
`lynx http://localhost:6000`.

-----


<!--<div class="message">
  Howdy! This is an example blog post that shows several types of HTML content supported in this theme.
</div>

Cum sociis natoque penatibus et magnis <a href="#">dis parturient montes</a>, nascetur ridiculus mus. *Aenean eu leo quam.* Pellentesque ornare sem lacinia quam venenatis vestibulum. Sed posuere consectetur est at lobortis. Cras mattis consectetur purus sit amet fermentum.

> Curabitur blandit tempus porttitor. Nullam quis risus eget urna mollis ornare vel eu leo. Nullam id dolor id nibh ultricies vehicula ut id elit.

Etiam porta **sem malesuada magna** mollis euismod. Cras mattis consectetur purus sit amet fermentum. Aenean lacinia bibendum nulla sed consectetur.

## Inline HTML elements

HTML defines a long list of available inline tags, a complete list of which can be found on the [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/HTML/Element).

- **To bold text**, use `<strong>`.
- *To italicize text*, use `<em>`.
- Abbreviations, like <abbr title="HyperText Markup Langage">HTML</abbr> should use `<abbr>`, with an optional `title` attribute for the full phrase.
- Citations, like <cite>&mdash; Mark otto</cite>, should use `<cite>`.
- <del>Deleted</del> text should use `<del>` and <ins>inserted</ins> text should use `<ins>`.
- Superscript <sup>text</sup> uses `<sup>` and subscript <sub>text</sub> uses `<sub>`.

Most of these elements are styled by browsers with few modifications on our part.

## Heading

Vivamus sagittis lacus vel augue rutrum faucibus dolor auctor. Duis mollis, est non commodo luctus, nisi erat porttitor ligula, eget lacinia odio sem nec elit. Morbi leo risus, porta ac consectetur ac, vestibulum at eros.

### Code

Cum sociis natoque penatibus et magnis dis `code element` montes, nascetur ridiculus mus.

{% highlight js %}
// Example can be run directly in your JavaScript console

// Create a function that takes two arguments and returns the sum of those arguments
var adder = new Function("a", "b", "return a + b");

// Call the function
adder(2, 6);
// > 8
{% endhighlight %}

Aenean lacinia bibendum nulla sed consectetur. Etiam porta sem malesuada magna mollis euismod. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa.

### Lists

Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Aenean lacinia bibendum nulla sed consectetur. Etiam porta sem malesuada magna mollis euismod. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.

* Praesent commodo cursus magna, vel scelerisque nisl consectetur et.
* Donec id elit non mi porta gravida at eget metus.
* Nulla vitae elit libero, a pharetra augue.

Donec ullamcorper nulla non metus auctor fringilla. Nulla vitae elit libero, a pharetra augue.

1. Vestibulum id ligula porta felis euismod semper.
2. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.
3. Maecenas sed diam eget risus varius blandit sit amet non magna.

Cras mattis consectetur purus sit amet fermentum. Sed posuere consectetur est at lobortis.

<dl>
  <dt>HyperText Markup Language (HTML)</dt>
  <dd>The language used to describe and define the content of a Web page</dd>

  <dt>Cascading Style Sheets (CSS)</dt>
  <dd>Used to describe the appearance of Web content</dd>

  <dt>JavaScript (JS)</dt>
  <dd>The programming language used to build advanced Web sites and applications</dd>
</dl>

Integer posuere erat a ante venenatis dapibus posuere velit aliquet. Morbi leo risus, porta ac consectetur ac, vestibulum at eros. Nullam quis risus eget urna mollis ornare vel eu leo.

### Tables

Aenean lacinia bibendum nulla sed consectetur. Lorem ipsum dolor sit amet, consectetur adipiscing elit.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Upvotes</th>
      <th>Downvotes</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <td>Totals</td>
      <td>21</td>
      <td>23</td>
    </tr>
  </tfoot>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>10</td>
      <td>11</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>4</td>
      <td>3</td>
    </tr>
    <tr>
      <td>Charlie</td>
      <td>7</td>
      <td>9</td>
    </tr>
  </tbody>
</table>

Nullam id dolor id nibh ultricies vehicula ut id elit. Sed posuere consectetur est at lobortis. Nullam quis risus eget urna mollis ornare vel eu leo.
-->

