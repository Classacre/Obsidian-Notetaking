# Obsidian-Notetaking
My iteration of what I believe is the most efficient and automated note taking system which leverages the Obsidian app and its plugin ecosystem. This note taking system aims to replace Zotero, Anki, Your Web Browser, Calendars, To do lists, Mindmaps and much more in one single application. I believe that by incorporating everyting into one system / application we greatly increase productivity.

These notes were designed with the PARA method (by Tiago Forte) in mind instead of Zettelkasten (popularized by Niklas Luhmann), hence, these notes are structured around projects, areas, resources and archives (PARA). Zettelkasten may be used after the PARA method for consolidation of concepts. See image below.

![Elevate Note Taking Workflow](https://github.com/Classacre/Obsidian-Notetaking/assets/40638863/9bcccaa3-c90f-4091-8197-bbcdf4230283)

# Purpose of this Git Repository
Serves as a backup of my Obsidian vault in the rare event that Obsidians built in syncing system fails or is discontinued. Additionally serves as documentation for people who wish to build an efficient and automated note taking system simmilar to what I have developed through the obsidian app.

# Community Plugins Used
All of the plugins are available via the Obsidian community plugins browser - B.R.A.T is no longer required to update community plug-ins due to official added support for each plugin, however, corresponding github links are given where appropriate. These plugins aim to streamline note-taking and automate tasks to ensure an unparallel note-taking experience. A brief description of the usecases of each plugin are given after the corresponding list.

- Advanced Tables
- Custom Frames
- Dataview
- Discord Rich Presence
- ePub Reader
- Git
- Homepage
- Hover Editor
- Imgur
- Metadata Menu
- Minimal Theme Settings
- Omnisearch
- PDF++
- Share Note
- Smart Second Brain
- Spaced Repetition
- Surfing
- Tasks
- Templater
- Text Extractor

## Advanced Tables
Aims to improve QOL (Quality of Life) of table making by incorporating navigation and transformation hotkeys which are lacking in base obsidian - this greatly speeds up table creation and formatting. Additionally, this plugin introduces a simple table formatting UI which removes the requirement of manually coding the tables formatting and allows for pressing of a button to format the table. Additonally adds excel formulas and capabilities to tables.

## Custom Frames
Aims to replace the users web browser by incorporating iFrames into Obsidian. This is a personal addition which I use to combat procrastination by removing distractions caused by surfing the web. The user implemented iFrames retain cookies which allow the user to open and close specific websites as if they were on a web browser but does not allow for notifications from other websites and the freedom of changing websites (unless it is through links from the implemented iFrames). The use case of this plugin is mainly for accessing the schools website, unit lectures and ChatGPT. These iFrames are opened within obsidian as a side tab, split tab or a pop out window.

## Dataview
The automation core of Obsidian, allows for automation and querying of data in the entire Obsidian vault through javascript queries. Used for adding classes to notes, adding properties to notes and much more. This plugin is essentially the "backend" of automation for the vault.

## Discord Rich Presence
A personal addition. Adds a custom activity status on Discord to showcase what I am doing in the obsidian vault. Informs others that im busy studying.

## ePub Reader
Allows for reading of .epub files in Obsidian - removes the need for opening an alternative app to read ePubs, a personal addition to remove possible procrastination caused by leaving the app.

## Git
Used to automatically backup the vault to git. Used in addition to Obsidians paid syncing service (Obsidian Sync) but may also be used as a standalone syncing service with no space limitations.

## Homepage
Plugin used to redirect the user to the home.canvas of the Obsidian vault. Showcases the planner, tasks and todo list automatically. Important for keeping track of tasks and time.

## Hover Editor
The custom "speed" component of this Obsidian vault. Allows for direct editing of notes via a hover editing functionality. Allows for incredibly quick note taking when used in tandem with PDF++ and Custom Frames. 

## Imgur
Automatically uploads images to imgur to save space - downside is the requirement of an internet connection to load images.

## Metadata Menu
The visual aspect of the homepage (home.canvas). Visualizes tasks, due dates, calendar and vault sections and facilitates ease of access by creating interactible and editable menus in notes and canvas. Uses a combination of dataview, callouts and javascript queries to run a simple and dynamic homepage (home.canvas)

## Minimal Theme Settings
Allows for editing of the minimal theme - table settings changed to wider format to better facilitate the Advanced Tables plugin.

## Omnisearch
Automates OCR for PDF files and images by using the Text Extractor plugin as a dependancy. Allows for searching of both notes and files through a single search query and ranks them based on a custom indexing algorithm. Mainly used for OCR automation.

## PDF ++
The core note taking plugin. Fundamentally replaces Zotero in its entirety and implements Zotero functionality into Obsidian. Allows for creation of .md file compatible callouts and creates a blazing fast note taking method when incorporated with the Hover Editor plugin. Allows for creation of callouts to segments within a PDF file, replaces the need for copy pasting unlinked screenshots of PDF parts, works incredibly well with the Spaced Repition by linking "images" to its appropriate page and section in a PDF.

## Share Note
Allows for sharing a specific note online while maintaining .css styles from the obsidian theme. Simply allows sharing of notes to other people without exposing the entirety of the vault unlike Obsidian Publish.

## Smart Second Brain
AI component of this vault. Allows for RAG lookup of notes and documents. Also doubles as an AI assistant (may be used in tandem with RAG feature or as standalone). Works completely offline through OLlama. May be used online through OpenAI API's. May be used as an AI search engine via its text-encoding feature.

## Spaced Repition
Scientifically backed repetition algorithm for memorization of concepts. Replaces Anki. Works with callouts (helpful when linking a callout to a lecture diagram through PDF++).

## Surfing
Opens all hyperlinks and web based links in Obsidian by hijacking its callout feature. Aims to remove need to use a web browser for studying. Works simmilarly to the Custom Frames plugin but affects links.

## Tasks
Allows for creating tasks/ to-do lists with added information such as date of creation, date of completion, status, priority. Works in tandem with Dataview and Metadata tables plugin to update homepage (home.canvas) helps keep track of tasks and time.

## Templater
Automated tempalting of notes based on class defined in Class folder (created by dataview and metadata tables). Speeds up note taking process by adding properties to notes which allows for data querying via javascript queries, metadata menu and dataview.

## Text Extractor
Dependancy for Omnisearch. Automatically OCRs PDF's and Images without the need to use an external app like Adobe Acrobat DC.