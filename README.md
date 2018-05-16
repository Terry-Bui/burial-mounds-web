# Introduction
This is a template that uses Jekyll and based off feeling-responsive theme by Phlow. To get the basic structure, please follow the steps in this README. It is assumed that the user have basic knowledge of jekyll, if not, please refer to https://jekyllrb.com/docs/home/ for more information. The purpose of this project is to generate a data-driven website from a CSV. For the purpose of this project, each row in the CSV will be considered a _record_ and a page for each record will be called a _Record Page_ and that a _post_ in jekyll is in our case a _Record Page_.

## Prerequisites
Need to have Python installed to run the script and also have Jekyll installed to test the website locally. It is also assume that you are using Ubuntu if you wish to test your website locally before uploading to Github Pages.

## Running Jekyll locally
Assuming jekyll is installed, to see your website locally, open up the terminal at the root of the folder of the project. Type in the following command then press center

`bundle exec jekl serve --config _config.yml,_config_dev.yml`

## Configuration
The \__config.yml_ file contains configuration options for the website. For basic configuration, do the following steps.

1. Open the \__config.yml_ file and work through it. The main key:value pair for basic customisation is _title_, _slogan_, _descripton_, _credits_, _author_, _url_, _baseurl_. More details can be found on https://help.github.com/articles/configuring-jekyll/ and https://jekyllrb.com/docs/configuration/.
	* *NOTE*: If you are running jekyll locally to see your changes, please stop the server then run it again to see changes made in your _\_config.yml_ file
	* If the user wish to add more custom data that can be accessed throughout the site via the Liquid templating system, then rather adding it directly into \__config.yml_ file, users are recommended to add it into the _additional\_config.yml_ file which is located in the \__data directory.
	* For example the variable _google-map-marker_ can be accessed via `site.data.additional_config.google-map-marker`.
	* More information is contained here: https://jekyllrb.com/docs/datafiles/

2. Add your _logo.png_ (if you have one) to _/assets/img/_.

3. Open \__data/socialmedia.yml_ and add your own social media links.

4. Open \__data/navigation.yml_ and customize your navigation.
	A navigation link composed of components, _title_, _url_, _side_.
	_title_ is the name of the link
	_url_ is the relative link to the page.		
	_side_ is whether the link would be located on the left-hand or right-hand side of the navigation bar. It is recommended to put it all in the left hand side to keep the *Search* bar at its current location.

5. Open \__data/language.yml_ and translate the theme if necessary.

6. Open \__data/services.yml_ and customize links in the footer.

7. Open \__data/network.yml_ and customize links in the footer.

8. Open \__data/authors.yml_ and edit author information and set default author in config.yml.

	* Addtion scss files can be made in the \__sass_ folder. You need then need to import the file in assets/css/styles_feeling_response.scss, for it to be included in the future build. For example, if you make a sass file called _tabs.scss_ in the \__sass_ folder then, inside the assets/css/styles_feeling_response.scss file, you would write

9. Open _\_layout/record.html_ and customize the template, which will be used for the Record pages.


# Preprocessing
Before auto generating record pages, ensure your data is well-formed for minimal hassle. A record should have a column that contains the UUID (unique identifer) for that record.

# Customization
Before auto generating record pages, the user can customize the site. This section will discuss some customisations available to the user.

## Editing Record pages template
The record pages uses _\_layout/record.html_ as the template, to modify the template, please see the [Components of the Record page template section](#components-of-the-record-page-template) first.

## Merging two csv together
A python script written in python3 have been provided to merge two csv file based on a key.

## Auto-generate page for each row
In the _\_import_ folder there is a script that have been provided. The purpose of the script is to generate a seperate yaml file for each row in the csv. Each column correspond to a _key:value_ pair in the yaml file, the key is name of the column with all space characters replaced with the '\_' character and the characters are coverted to all lowercase.

For example, in the csv there is 3 columns, '_TRAP ID_', '_Max diameter_', '_Surrounding Land Use_' and the values are, 1000, 34, Annual Agriculture for a row. then in the corresponding Record Page for that row, the Front Matter variable will be in the following format

```
trap_id: '1000'
max_diameter: '34'
surrounding_land_use: Annual Agriculture
```

Dependencies required:
ruamel.yaml


## Map functionality
The javascript required for the google map functionality for each Record page is located _\_includes/additional\_helper/additional\_footer.html_ file. It uses Liquid templating and only apply the javascript for Record Pages. In the front matter for the Record Pages, the way it does this that it uses liquid tags to see if the current page is a post.

The default setting for the Map functionality is that it uses the _latitude_ and _longitude_ Front matter variable of the Record Page. So if users wish to have a map, the column name in the csv must also correspond to that name.

*NOTE*: It is fine in the CSV to have the column name "Latitude" and "Longitude" because the script that generate a page for each row then converts the column names to lowercase when it passes it into the Front Matter for the Record Page.
The format of the coordinates should also be in _decimal degrees_ which is what Google Map API uses.

The _google-map-marker_ variable is in the _config.yml_ file and is used to determine whether to add a marker that points to the location of the record, if the user do not wish to have a Google Map marker then the user can edit the _google-map-marker_ variable and change the value from _true_ to _false_ and if they wish to have the marker back then they would reverse it, changing the value from _false_ to _true_. The _title_ variable is used as the title of the Google Map marker.

*NOTE*: If the user make changes to the

If users do not wish to further customize the functionality, they can skip the rest of the section.

The following code is the Javascript code to use Google Maps API

```javascript
<script>
	function initMap() {
	 var latLon = {lat: parseFloat('{{ page.latitude }}'), lng: parseFloat('{{ page.longitude}}')};

	 var map = new google.maps.Map(document.getElementById('map'),{
		 center: latLon,
		 zoom: 8
	 });
	 if('{{ site.data.additional_config.google-map-marker }}' == 'true'){
		 var marker = new google.maps.Marker({
				position: latLon,
				map: map,
				title: '{{ page.title}}'
			});
		}
	}
</script>
```

It uses the page Front Matter variables, _title_,_latitude_, _longitude_ and also the site Front Matter variable _google-map-marker_

_latitude_, _longitude_ are stored in a a Javascript dictionary
 _title_ is for the title of the marker where the marker points to the location of the record
 _google-map-marker_ is to determine whether to have a Google Map Marker that marks the coordinates of the Record Page


## Adding additional stylesheet
In the _\_includes/helper_ directory, there is a html file called _head.html_, this contains all the default information about the page inside the <head> tags, another file named _additional\_head.html_ file which is located in the _\_includes/addition\_helper_ directory.
If  user wish to add a additional css spreadsheet or other additional information that they wish to enclose inside the <head> tag of the website, it is recommended to add it into the _additional\_head.html_ file so there is no confusion between the default metadata for the template and the new metadata defined by the user, the additional metadata added will be after the default metadata because we are using jekyll `include` tag to include the content from _additional\_head.html_ in the _head.html_ file.
The user is also free to modify the _head.html_ file. Users wishing to add more _Javascript_ should refer to the *Adding additional Javascript* section in this README.

## Adding additional Javascript
In the _\_includes/helper_ directory, there is a html file called _additional\_footer.html_, users are recommended to put additional javascript in this file. If users wish to add _Javascript_ that uses liquid tags then please add the _Javascript_ inside the file, enclosed within _script_ tags.

For example the javascript code that uses Google Maps API to generate maps for record for the Burial Mounds website which uses liquid tags to access *Front Matter* variables.

```javascript
<script>
	function initMap() {
	 var latLon = {lat: parseFloat('{{ page.latitude }}'), lng: parseFloat('{{ page.longitude }}')};

	 var map = new google.maps.Map(document.getElementById('map'),{
		 center: latLon,
		 zoom: 8
	 });
	 if('{{ site.data.additional_config.google-map-marker }}' == 'true'){
		 var marker = new google.maps.Marker({
				position: latLon,
				map: map,
				title: '{{ page.title}}'
			});
		}
	}
</script>
```
The Front Matter variables are _page.title_, _page.latitude_, _page.longitude_ and these variables for the Page. So each Page front matter will have variables with that name but possibly different value. For example, the Mound with TRAP ID 1001 have a latitude of 42.627103 and 25.246605 while the Mound with id 1002 have latitude 42.626585 and longitude 25.250297. So the _latitude_ value will be 42.627103 on the page for Mound with id 1001 and the _latitude_ value will be 42.626585 for the Mound with id 1002

## Recompile changes
Because this is a static website, everytime users wish to make push their changes (assuming the website is on Github):

1. Delete the _\_posts_ folder
2. Recompile the record pages by running the _makeSite.py_ Python script and additional scripts if needed
3. git add then commit then push it to the remote repo

# Important Pages

## Collections

The _\_pages/pages-root-folder/collections.md_ is the web page that is used to display the links to all the record pages. The organization of the pages is grouped by *categories* where one tab correspond to a category. A record page can appear in one or more category. To add a record page to belong to a category, add the name of the category in the YAML list for that record page under the _categories_ key.

If user wish to change the content of the page, they can edit _\_pages/pages-root-folder/collections.md_.

## Templates of Record Pages

The yaml template for Record pages is the _\_import/template.yaml_ file. The auto generate script will use that as a template for the Record pages.
The layout file that _\_import/template.yaml_ uses is the _\_layout/record.html_ file.

### Components of the Record page template

![Overview of record page](README_screenshots/record_page_overview.png)
