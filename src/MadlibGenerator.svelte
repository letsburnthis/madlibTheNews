<script>
    //npm install compromise
    import nlp from 'compromise';
    export let textToTransform='';
	export let madlibCreated =0;
	let nouns;
    let replaceNum = 10;
	let verbs=[];
	let adjectives;
	let wordList=[];
	let madlibList=[];
	let inProgressMadlib;
	let finalMadlib = "";


    function createMass(words){
		let mass = nlp(words);
		return mass;
	};

    function createArrayOfNouns(mass){
		nouns = mass.nouns().out('array');
		nouns = nouns.map(function(noun){
			console.log(noun);
			if(noun.slice(-1)=='s'){
				noun = noun+'np';
				return noun;
			}
			else{
				noun = noun+'ns'
				return noun;
			}
		});
		console.log("check this" +nouns);
		
    };
    

    function createArrayOfverbs(mass){
		let allVerbs = mass.verbs().out('array');
		
		let pastTenseCheck = mass.verbs().toPastTense().all().text();
		//let presentTenseCheck = nlp(words).verbs().toPresentTense().all().text();
		//future/infinitive just ends up being a standard verb as far as I can tell
		//let futureTenseCheck = nlp(words).verbs().toFutureTense().all().text();
		//let infinitiveTenseCheck = nlp(words).verbs().toInfinitive().all().text();
		//console.log(presentTenseCheck)
		for(let verb of allVerbs){
			console.log(verb);
			
			if(pastTenseCheck.includes(' '+verb+' ')){
				verbs.push(verb+'vp')
			}
			//else if (presentTenseCheck.includes(' '+verb+' ')){
			//	verbs.push(verb+'vr')
			//}
			//else if (futureTenseCheck.includes(' '+verb+' ')){
			//	verbs.push(verb+'vf')
			//}
			//else if (infinitiveTenseCheck.includes(' '+verb+' ')){
			//	verbs.push(verb+'vi')
			//}
			else if(verb.slice(-3)=='ing'){
				
				verbs.push(verb+'vg')
			}
			else if(verb.slice(-1)=='s'){
				
				verbs.push(verb+'vs')
			}
			else {
				verbs.push(verb+'vv')
			}

		}

		console.log(verbs);


    };
    

    function createArrayOfAdjectives(mass){
		//need to create something that recognizes if word ends in 'st'
		adjectives = mass.adjectives().out('array');
		adjectives = adjectives.map(adjective => adjective+'aa');
    };
    
    
    //switched from randomly selecting words to add to a list to randomly choosing to remove words from a list
	//far more efficient because you can start by removing duplicates rather than checking for duplicates every time
	// with an increasing likelyhood of duplicates as the list fills
	// and no longer have to worry about source list running out of words
	function populatewordList(){
		//turns the three arrays into single new array
		wordList = nouns.concat(verbs,adjectives);
		//converst array to Set and then back (aka removes duplicates)
		wordList = Array.from(new Set(wordList));


		while (wordList.length> replaceNum){
			let n = Math.floor(Math.random()*wordList.length);
			//removes array element from the location
			wordList.splice(n,1);
			console.log(wordList);

		}
    };
    


    function createMadlib(){
		let n=0
		
		while(n<wordList.length){
			madlibList.push(document.getElementById(n).value);
			console.log(document.getElementById(n).value)
			n=n+1;
			console.log('cycle '+n+' complete')
		}
		createFinalMadlib();

	};



    function createFinalMadlib(){
		let n=0
		inProgressMadlib = textToTransform;
		while (n<wordList.length){
			//drops last letter of word
			let toBeReplaced = new RegExp(wordList[n].slice(0,-2),'g');
			//replaces all instances of word with madlib replacement
			
			inProgressMadlib = inProgressMadlib.replace(toBeReplaced, '<u>'+madlibList[n]+'</u>')
			n=n+1;
		}

		finalMadlib=inProgressMadlib;
		madlibCreated = 1;
    };
    
    function program(words){
		console.log(words);
		let mass = createMass(words);
		createArrayOfNouns(mass);
		createArrayOfverbs(mass);
		createArrayOfAdjectives(mass);
		populatewordList();
		console.log(wordList);
		
    };
    
    
	function checkForWords(){
		console.log(textToTransform);
		if(textToTransform!=''){
			clearInterval(check);
			program(textToTransform);
			
		}
	
	};

	let check = setInterval(checkForWords,10);
	



</script>




<ul>
	{#each wordList as word, i}
		<li>
			<label for={i} >
				{#if word.slice(-2)=='ns'}
					noun singular
				{:else if word.slice(-2)=='np'}
					noun plural
				{:else if word.slice(-2)=='aa'}
					adjective
				{:else if word.slice(-2)=='vv'}
					verb
				{:else if word.slice(-2)=='vp'}
					verb (past tense)
				{:else if word.slice(-2)=='vg'}
					verb (ending in 'ing')
				{:else if word.slice(-2)=='vs'}
					verb (ending in 's')
				{/if}
			</label><input id={i} >
		</li>
	{/each}
</ul>

<button on:click={createMadlib}>Press when done!!!</button>

<p>{@html finalMadlib}</p>