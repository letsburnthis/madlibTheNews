<script>
	//if I cared about memory usage I would probably just overwrite the same object each time since I don't need previous data
	//also, do () in regular expressions make it save something or another?
	
	let linksMess = "";
	let links = "";
	let randomLink = "";
	export let articleText = "";
	export let headline = "";
	//for posterity, same thing using promises
	//var request = new Request('https://www.cnn.com');
	//fetch(request).then(function(response) {
  	//return response.text();
		//}).then(function(text) {
		  //full cnn home page html
		  //cnnSite = text;
		  //gets section of html for breaking news
		  //linksMess = cnnSite.match(/CNN - Breaking News, Latest News and Videos(.*?)registryURL/g);
		  //gets links that don't start with video
		  //links = linksMess[0].match(/"uri":"\/20(.*?)index.html/g);
		  //selectes a random link from above
		  //randomLink = links[Math.floor(Math.random()* links.length)];
		  //corrects it so that link can be followed
		  //randomLink = randomLink.replace('"uri":"','https://cnn.com');
		//});
		
	async function getFullHTML(site){
		let response = await fetch(site);
    	let data = await response.text();
    	return data;
	};


	function getLinkFromHome(homeHTML){
		//gets section of html for breaking news
		linksMess = homeHTML.match(/CNN - Breaking News, Latest News and Videos(.*?)registryURL/);
		//gets links that don't start with video
		links = linksMess[0].match(/"uri":"\/20(.*?)index.html/g);
		//selectes a random link from above
		randomLink = links[Math.floor(Math.random()* links.length)];
		//corrects it so that link can be followed
		randomLink = randomLink.replace('"uri":"','https://www.cnn.com');
		return randomLink
	};
	//need to learn promises or async or something to be able to force getLinksFromHome to wait for getFullHTML to complete.

	function getHeadline(articleHTML){
		//pulls out title html
		let headlineHTML = articleHTML.match(/<title>(.*?) - CNN/);
		//trims title html to just text
		headline = headlineHTML[0].slice(7, -6);
		return headline;
	};

	function getArticleText(articleHTML){
		//gets roughly first paragraph html, for some reason there's more than two in the article html???
		articleText = articleHTML.match(/<cite class="el-editorial-source">(.*?)"zn-body__read-more">/)[0];
		//gets rid of html css stuff
		articleText = articleText.replace(/<(.*?)>/g,' ')
		//gets rid of html link stuff
		articleText = articleText.replace(/{(.*?)}/g,' ')
		return articleText
	};
	

	async function experiment(){
		let text = await getFullHTML('https://www.cnn.com');
		let link = await getLinkFromHome(text);
		articleText = await getFullHTML(link);
		headline = await getHeadline(articleText);
		articleText = await getArticleText(articleText);
		return articleText;
	};

	experiment();


	

	

	
 
	
	
	
	
</script>



