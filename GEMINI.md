# Project Overview

This is a personal blog created with [Hexo](https://hexo.io/), a fast, simple, and powerful blog framework. The blog is authored by Ningbo Yang and titled "Ningbo's blogs". The content focuses on topics like ROS, Ubuntu, TensorFlow, Gazebo, Rviz, robotics, and reinforcement learning. The site uses the "next" theme.

## Building and Running

This project uses Node.js and Hexo. The following commands are essential for working with the blog:

* **Install dependencies:**

    ```bash
    npm install
    ```

* **Create a new post:**

    ```bash
    npx hexo new "My New Post"
    ```

* **Run the local development server:**

    ```bash
    npx hexo server
    ```

    The blog will be available at `http://localhost:4000`.

* **Generate static files:**

    ```bash
    npx hexo generate
    ```

* **Deploy the blog to GitHub Pages:**

    ```bash
    npx hexo deploy
    ```

## Development Conventions

* Blog posts are written in Markdown and are located in the `source/_posts` directory.
* The configuration for the Hexo site is in `_config.yml`.
* The configuration for the "next" theme is in `themes/next/_config.yml`.
* The site is deployed to the `master` branch of the `ningbo6/ningbo6.github.io` repository.
