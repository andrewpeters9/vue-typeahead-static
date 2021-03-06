# vue-typeahead-static

This typeahead was created to fill a need: all existing Vue typeaheads were either heavy, full-featured behemoths, or required an API request to retrieve data.

Credits to trionfo1993 for inspiration and the code for some of the events.

This component has been built for *Bootstrap 4.* I haven’t tested it on Bootstrap 3, but I would assume it would work OK given a few tweaks.

[An image of what it looks like.](https://gyazo.com/acaf451be6b03738539a96423e30ed92)

## Installation instructions

To install the program:

```
npm install vue-typeahead-static --save
```

To include the component:

```
import TypeAhead from 'vue-typeahead-static';
```
and somewhere in your main.js/app.js file...
```
Vue.component('TypeAhead', TypeAhead);
```

You're then free to use the component like so:
```
<TypeAhead v-model="subject" inputClasses="taller" :data="subjects" :tabToSelect="true"></TypeAhead>
```

## Configuration
Note that vue-typeahead-static accepts *v-model*

### Required
* *data* - Array (of Strings) - Data for the component to work with

### Optional
* *placeholder* - String - A placeholder for the input
* *wrapClasses* - String - Classes to be attached to the div which wraps the input. Classes to be space-joined, e.g. ‘form-control input-lg’
* *inputClasses* - String - Classes to be attached to the input. Classes to be space-joined, e.g. ‘form-control input-lg’
* *maxResults* - Number - default: 10 - The maximum amount of results to be displayed at one time
* *searchMin* - Number - default: 2 - The minimum amount of characters required before results will begin to show
* *startValue* - String - A value for the input to start off with
* *tabOnEnter* - Boolean - default: true - Whether or not the component will ‘tab’ off focus to the next ‘tab-able’ element
* *tabToSelect* - Boolean - default: false - Whether or not tabbing a selection will result in selecting it, or just tabbing to the next item
* *noResultsText* - String - default: ‘No results found.’ - A string to be shown when there are no remaining results.

Note that there is a tabbing fix implemented, but it won’t work if there are tab indexes in your form.