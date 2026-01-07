# SWEN-course-page-migration

This is the project to migrate the course pages for the RIT Software Engineering Masters program classes from raw HTML to the Hugo framework. Hugo uses a template system to allow for fast and easy additions or changes to a static site, allowing for improved maintainability. The process includes both updating outdated layouts to improve site flow, and the implementation of reusable components and templates to improve maintainability.

## Adding new content pages

New content pages can be added by using the `hugo new content` command in the site's directory, including the target directory and name for the page content file. For example:

```
hugo new content topics/new-topic.md --kind topic
```

The above example will create a content file called `new-topic.md` in the `content/topics` directory using the site's `topic.md` archetype. Omitting `--kind topic` from the command will create the content file using the Book theme's default archetype instead

## Testing

Hugo uses a series of layouts and content markdown files to create web pages. In order to see what the website looks like, you will need to have Hugo build the site on a local server. You can use either of the following console commands to build and start the server, run from indside the site's directory within the repo. Note that Windows user should not run commands in Command Prompt or Windows PowerShell.

```
hugo server -D
```
or
```
hugo server --buildDrafts
```

This will start a server that includes content that is flagged as a draft. If you don't want to include draft content, use `hugo server` instead.

## Site Navigation

The site will automatically create a table-of-contents style navigation mention on the left side of the page based on the filestructutre of the `content` directory. Content files located in `content/docs` will be included in the menu, and the menu will reflect the structure of the `docs` directory (including subdirectories). The content of `docs` will be the first level of the menu, while the content of subdirectories will be subsequent levels.

Note: subdirectories need an _index.md file containing a title in the frontmatter, but do not require any content. Adding content to these _index.md files will make that part of the menu a clickle link, otherwise it will just be a title with other content pages in that directory underneath it.

### Page Navigation

The site will also create a table-of-contents for each page using the second and third level headings of the markdown. This table of contents is on by default, and pages that do not need it (or need to turn it off for extra horizontal space) should add `ToC: false` to their frontmatter.

## Hugo Components

### Content

Most of the pages on the site will be content markdown files organized into several subdirectories of the primary content directory. The book theme uses the content files in the “docs” directory to construct the site’s navigation menu, and so important pages such as the Syllabus and Project page will be in their own subdirectories within “docs.” Other types of content will be organized in their own subdirectories outside of the “docs” directory. Due to how the book theme functions, the primary content files of subdirectories within the “docs” directory will all be index.md files. This is necessary for the theme to recognize which is the main file of the directory and structure the navigation menu accordingly.

### Layouts

Layouts contain the html that defines the structure of different pages, and will be provided by the book theme. See the Theme subsection for more information.

### Partials

Partials allow small reusable sections of html to be included in a layout file, and will be provided by the book theme. See the Theme subsection for more information.

### Archetypes

Archetypes define the initial state of a newly created content file, allowing you to automatically fill out certain parts of the front matter or markdown content. The default single page archetype provided by the book theme is likely sufficient for many pages, such as project pages, as they are not similar enough in the structure of their content to make effective use of archetypes. However, a custom single page archetype will be used instead as it is useful for customizing commonly used front matter settings. There will also be an archetype for topic pages, as they all have similar structures.

### Resources

Internal resource files, such as .pptx files or images, will be stored in Hugo’s “static” directory.

### Shortcodes

Shortcodes allow small reusable sections of html to be included in a markdown file, and are mostly be provided by the book theme. See the Theme subsection for more shortcodes from the book theme.

There are currently 7 shortcodes available in the site template: flex-table and snippet

#### flex-table

This shortcode allows you to create a table using a normal markdown list. The table will collapse into a list format on smaller screens. There are several parameters available.

**cols and advanced-cols**: This is the only required parameter, and only one of the two should be used. "cols" will simply designate the number of columns in the table, an give column an equal width. ie: cols="3" will create a table with 3 columns, each with a width of 1fr. "advanced-cols" allows you to designate the widths of each column specifically. ie: advanced-cols="4em 1fr 2fr" will create a table with 3 columns, eahc with their designated width.

* cols="x"
* advanced-cols="x y z"

**title**: This paramter sets the "id" paramter of the table's enclosing div. This is primarily useful for adding additional css unique to a specific table.

* title="id"

**headings**: This paramter sets the names to use for each section of the table when it is collapsed to a list view by adding a style tag just before the table's enclosing div, and requires the "title" paramter is set. The provided headings do not appear in the normal table view, only the list view. setting the parameter as heading="Week;Topic;Assignments" will add a heading with those names before the related content of each section of the collapsed list view.

* headings="x;y;z"

#### cell-color

This will color the background of a cell in the flextable shortcode, and accepts any valid css value for `background-color`.

It should be used in-line with the content of the cell:

```markdown
- * normal cell
  * {{% cell-color color="rgba(0, 0, 255, .5)" %}}blue cell with tranparency{{% /cell-color %}}
  * normal cell
  * {{% cell-color color:"red" %}}red cell without transparency{{% /cell-color %}}
```

#### columns

This shortcode will organize a list of content into up to 3 columns

```markdown
{{% columns %}}
- left

- middle

- right
{{% /columns %}}
```

#### hint

Creates a hint/notification block to display content

```
{{% hint warning %}}
content goes here
{{% /hint %}}
```

there are 5 colors for hint blocks: info (blue), success (green), warning (yellow), danger (red), important (purple)

#### snippet

This shortcode allows you to reuse text across multiple pages. Put the reused content in a markdown file inside the site's `assets` folder, then call the snippet shortcode where the content needs to go.

For example, if you have two pieces of reusable content in `assets/snippets`:

```markdown
# Title

## Section 1

{{% snippet src="snippets/reusable-A.md" %}}

## Section 2

some page-specific content

## Section 3

{{% snippet src="snippets/reusable-B.md" %}}
```

#### steps

This sortcode styles an ordered list so that the numbers on the list are connected to help keep larger step-by-step instructions organized

```markdown
{{% steps %}}
1. step 1

2. step 2

3. step 3
{{% /steps %}}
```

#### card

Adds a border around the inner content, and can optionally act as a link by using the `href` parameter

```markdown
{{% card %}}
some content inside a border
{{% /card %}}
```

**Note:** the card shortcode can behave inconsistently when it is inside of another shortcode

### Theme

We are currently using a slimmed down version of the Book theme (found [here](https://github.com/alex-shpak/hugo-book)), which consists of just the normal layout and relevant CSS.

## Hugo File Structure

The site’s files will be divided between the site’s main directories and the Book theme’s directories. For a visual example of the file structure using SWEN-610, see the diagram below

* Archetypes: 610/archetypes
* Content: 610/content
    * pages included in the ToC-style site navigation menu will be in the /docs subdirectory.
    * other content pages will be in their own subdirectories such as /assignments
* Resource Files: 610/static or 610/assets
* CSS: 610/assets/scss
* Resuable Content: 610/assets/snippets
* Layouts: 610/layouts
* Partials: 610/layouts/partials
* Shortcodes: 610/layouts/shortcodes

## Migration

For most pages, the migration process is very simple. It is usually just a matter of taking the content from the existing page and converting it into markdown inside of the page's content file in the content directory. However, complex (or large) tables may need to be replaced with an alternative layout. Shortcodes are very useful for this, as they allow a degree of additional control over the style of the page.

### Missing Pages

The following are pages that do not currently exist, or need extra review/modification after the migration process, and may need to be created from scratch:

* see readme for individual sites

New pages can be created either by creating a new markdown file in the appropriate directory, or configuring an archetype and using the `hugo new content` command (see Adding New Content Pages above)
