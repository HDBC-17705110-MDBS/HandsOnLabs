# How to contribute

This project is open to contributions. If you want to contribute, please follow the instructions below.

The link to the source code is:
[https://github.com/HDBC-17705110-MDBS/HandsOnLabs](https://github.com/HDBC-17705110-MDBS/HandsOnLabs)

## Contributing to the project

* **Fork the repository**: Click the **Fork** button at the top right of the page. This will create a copy of the repository in your GitHub account. See image:

* **Clone the repository to your machine**: Use Git to clone the repository you forked to your machine.

```bash
git clone forked_repository_url
```

* **Create a new branch**: Before making changes, create a new branch where you will make your modifications. This helps keep things organized. Use the following command:

```bash
git checkout -b branch_name
```

* **Make changes**: Make the necessary changes to the project files.

* **Add and commit the changes**: Use the following commands to add the changes and make a commit.

* **Push the changes to your GitHub repository** with the following command:

```bash
git push origin branch_name
```

* **Create a PR**: Go to your repository on GitHub and select the branch where you made the changes. A highlighted message will appear saying that you made a new branch. Click on "Compare & pull request" to start the PR.

You can also go to the new branch by clicking on the dropdown menu and selecting the branch you created **1** and clicking on the **New pull request** button **2**. See image:

* **Create a PR**: Provide a detailed description of the changes you made. You can also add screenshots or additional information to help the reviewers understand your changes. Click on **New pull request** and you will see a screen like the following:

* **Send the PR**: Once you have filled in all the information, click on the "Create pull request" button to send the PR to the original project.

* **Wait for the PR to be reviewed**: Once the PR is sent, the maintainers of the project will review your changes. They may ask for additional changes or approve the PR.

* **PR approved**: Once the PR is approved, the maintainers will merge your changes into the project.

* **PR rejected**: If the PR is rejected, the maintainers will provide feedback on the changes that need to be made. You can make the changes and send the PR again.

## How to build the book

### Installation of mdbook

To install mdbook, you first need to install Rust. You can find the installation instructions at [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

Once Rust is installed, you can install mdbook with the following command:

```bash
cargo install mdbook --vers 0.4.34
```

### Editing the files

The files are in Markdown format. You can find more information about the Markdown format at [Markdown Guide](https://www.markdownguide.org/basic-syntax/).

### Evaluate changes by generating the book in HTML format on your computer

```sh
mdbook serve --open
```
