# Command Line Tooling for Project Operations

> ***NOTE***: The Atomist Shell is in early internal-use mode.

## Concepts

Each shell session is stateful, maintaining a context of:

* **Loaded project operations**. Project operations (generators, editors and reviewers) may be loaded from:
 * The local file system. You can `git clone` a remote repository.
* **Directory defaults**: 
 * *Template base directory*: Directory under which templates are located by relative path.
 * *Output base directory*: Directory to which output projects are created, and in which projects to be edited are expected to reside.

## Atomist Shell Configuration

Place a file named `.atomist-profile` in the user home directory.

The contents should contain, as a minimum, the following paths to working directories:

```
template_root=/Users/russmiles/atomist/template-source
output_root=/Users/russmiles/atomist/output
```

> ***NOTE***: The CLI will not start up without a configuration file. Changes to configuration made during a session will not be written back to the configuration file. Please edit this file manually between sessions.

### *template_root*: The Template Working Directory

Templates will be loaded under this directory:

```
template_root=/Users/russmiles/atomist/template-source
```
Any templates added via CLI commands will be added automatically. If you add a template via a `git clone` operation, use the `reload` command within the shell to load it.

### *output_root*: Project Operations Output Directory

Output projects will be created under this directory:

```
output_root=/Users/russmiles/atomist/output
```

## Running the Atomist Shell from Binary

Download the distribution zip and run from the `/bin` directory. You may need to make the binary executable.

## Building and Running the Atomist Shell from Source

The Atomist Shell CLI project is built and run using `maven`.

The build relies on a local Maven repository with access to Atomist Artifactory artifacts.

The following from the command line to build and start the shell:

```
> > mvn compile exec:javaDexec.mainClass="com.atomist.projectoperation.cli.Main"
```

## Atomist Shell Commands

 Type `help` during shell execution for a list of commands. Important commands include:
 
 * `reload`: Reload project operations from known sources. Invoke this command after making changes to generators or editors.
 * `create`: Create a new project under the current output path. The shell will prompt for a template to use and for the parameters needed by that template.
 * `edit`: Edit an existing local project using a registered editor.
 * `edit-remote`: Edit an existing GitHub project using a registered editor. The edit will appear as a pull request in the repo.
 * `exit`: Exit the shell
 * `show`: Show the current state of the shell, including registered generators and editors.
 * `superfork`: Specify a GitHub repo containing a project that you wish to use as the basis for a template. (A seed project such as the `angular/quickstart` demo is ideal for this). Instead of a simple clone, the result will be a new directory under the current `template_root` containing a template that can create similar projects. You can then evolve the template to parameterize more parts of it: In the case of a JavaScript project, you would probably begin by turning `package.json` into a template by appending `_.mustache` to the filename and populating it using parameters you declare in `meta/info.yml`. See documentation on [template structure](/reference-docs/project-templates/project-template-contents-overview.md) for more information.
 * `template-synch`: Attempt to update the template with changes that have been made to the given project, which must have been created by the template. This allows for a workflow where a project is created from a template and then edited using standard tooling (such as an IDE) in order to identify updates for the upstream template.
 
## Getting out of trouble: *Cancelling a Command*

If you've kicked off a flow with a command and realise you no longer want to continue you can use the defactor standard `:q` command to get back to the `show` menu of commands.
 
## Suggested Workflow with the Atomist Shell

 1. `git clone` GitHub a template repository, which can include a generator and multiple editors, under the `template_root` directory specified in your configuration. Then work on the template locally.
 2. Use `create` and `edit` shell commands to create and work on generated projects. It's a good idea to `git init` these projects for easy diffing, even if you don't intend to push them.
 3. Use the shell's `reload` command when files change.
 4. Commit and push the template as necessary. 

As an alternative to step (1) above, you could begin by "superforking" an existing project on GitHub to create a template.

As an alternative to step (4) you could use the `smart-merge` command after editing a generated project.