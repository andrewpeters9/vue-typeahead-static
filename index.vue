<template>
	<div class="typeahead-wrap" :class="[wrapClasses]">
      	<input type="text" class="form-control"
      		autocomplete="off"
            spellcheck="off" 
      		:class="[inputClasses]"
            :placeholder="placeholder"
            :id="'type-' + identifier"
            @input="update()"
            @focus="focus()"
            @keydown.tab.prevent="handleTab($event)"
            @keydown.down="down"
            @keydown.up="up"
            @keydown.enter.prevent="enter($event)"
            v-model="query" />
		<div class="dropdown-menu" v-if="checkShow()">
			<a class="typeahead-item dropdown-item" href="javascript:;" 
				v-for="(match, index) in matches" 
				v-html="highlight(match)"
				:class="{active: index === current}"
				@click="linkClick(match)"></a>
			<div class="empty-search-text" v-if="!matches.length">{{noResultsText}}</div>
		</div>
	</div>
</template>

<script lang="babel">
export default {
	name: "TypeAhead",
	props: {
		placeholder: {
			type: String,
			required: false
		},
		wrapClasses: {
			type: String,
			required: false
		},
		inputClasses: {
			type: String,
			required: false
		},
		data: {
			//currently only accepts arrays of strings
			type: Array,
			required: true
		},
		maxResults: {
			type: Number,
			required: false,
			default: 10
		},
		searchMin: {
			//results won't appear until this minimum
			//is reached
			type: Number,
			required: false,
			default: 2
		},
		startValue: {
			//used for loading the typeahead with
			//value. Handy for when using url queries
			type: String,
			required: false
		},
		tabOnEnter: {
			//if true, we'll tab across to the next
			//tab-able item on enter
			type: Boolean,
			required: false,
			default: true
		},
		tabToSelect: {
			type: Boolean,
			required: false,
			default: false
		},
		value:{
			type: String,
			required: false,
			default: ''
		},
		noResultsText:{
			type: String,
			required: false,
			default: 'No results found.'
		}
	},
	data(){
		return {
			query: '',
			current: -1,
			matches: [],
			showMenu: false,
			identifier: Math.random().toFixed(6).slice(2,8)
			//identified is used to fix tabbing bug
			//i.e., two instances of the component
			//appear the same to isEqualNode
		}
	},
	watch: {
    	value: function (value) {
        	this.query = this.query !== value ? value : this.query
      	},
      	query: function (value) {
        	this.$emit('input', value)
      	}
    },
	methods: {
		handleTab: function(event){
			if(this.tabToSelect && this.showMenu && this.current !== -1){
				return this.selectWithTab(event);
			}
			this.tabToNext(event);
		},
		selectWithTab: function(event){
			this.query = this.matches[this.current];

			//hide menu and tab to next element
			this.showMenu = false
			if(this.tabOnEnter){
				this.tabToNext(event);
			}
		},
		tabToNext: function(event){

			this.showMenu = false;

			//note that this doesn't work with tab-indexes
			//allows fluid tabbing through

			const current = event.target;

			//find all tab-able elements
			const all = document.querySelectorAll('input, button, a:not(.typeahead-item), area, object, select, textarea');

			//int index
			let currentIndex;

			for(let i = 0; i < all.length; i++){
				//loop through all tab-able elements
				if(current.isEqualNode(all[i])){
					//if a match is found then assign index
					currentIndex = i;
					break;
				}
			}

			//focus the following element
			all[currentIndex + 1].focus();
		},
		linkClick: function(value) {
	        this.query = value;
	        this.showMenu = false;
	    },
		checkShow: function(){
			//check to see if the dropdown
			//should be shown

			//show if showMenu is true and query is long enough
			return this.showMenu && this.query.length >= this.searchMin;
		},
		pushResults: function(results){
			//called after the update function
			//pushes the filtered results
			this.current = -1;
			this.matches = results;
		},
		down: function(){
			//fired on keyDown for the input
			if (this.current < this.matches.length - 1) {
	        	this.current++
	        } else {
	        	//if there are no more matches, loop
	        	//active element back to the input
	        	this.current = -1
	        }
		},
		up: function(){
			if (this.current > 0) {
				//move current up
	        	this.current--
	        } else if (this.current === -1) {
	        	//loop the active element to the bottom match
	        	this.current = this.matches.length - 1
	        } else {
	        	this.current = -1
	        }
		},
		enter: function(event){
			//if the current element is the
			//input then do nothing
			if(this.current !== -1){
				//assign query
				this.query = this.matches[this.current];

				//hide menu and tab to next element
				this.showMenu = false
				if(this.tabOnEnter){
					this.tabToNext(event);
				}
			}
		},
		focus: function(){
			//fired when the input is clicked

			this.showMenu = true;
			this.update();
		},
		update: function(){
			//function is fired when the dropdown
			//items need to be reset

			if(this.query.length < this.searchMin){
				//if the query isn't long enough
				return this.pushResults([])
			}

			//filter the results, and then remove anything over max results
			let filtered = this.data.filter(entity => entity.toLowerCase().includes(this.query.toLowerCase()));
			filtered = filtered.slice(0, this.maxResults);

        	this.pushResults(filtered);
		},
		highlight: function(text){
			//takes text and wraps it with <b> tags
			//where query matches

			//create a case-insensitive regexp with query
			let re = new RegExp(this.query, 'ig');
			//find all matches
	        let instances = text.match(re);

	        //replaces the matches in the string
	        instances && instances.forEach(match => {
	        	text = text.replace(match, `<b>${match}</b>`)
	        })

	        return text
		}
	},
	mounted(){
		//sort the data A-Z on load, so that 
		//the results look better
        this.data.sort()

    	if(this.startValue){
    		//assign starting value
        	this.query = this.startValue;
    	}

    	document.addEventListener('click', (e) => {
	        if (!this.$el.contains(e.target)) {
	        	this.showMenu = false
	        }
	    })
	}
	 
}
</script>

<style scoped>
	div.dropdown-menu{
		display: block;
		right: 0px;
	}

	.typeahead-wrap{
		position: relative;
	}

	.empty-search-text{
		text-align: center;
		padding: .25rem 1.5rem;
	}
</style>