
### Create components from the data
On va ensuite boucler sur notre liste de films, et créer un composant pour chaque element de la liste.
**Tip:** 
Pour executer du code JS a l'interieur du code HTML, on utilise des accolades, ex:
```js
return ( 
	<> 
		{['1','2'].map(number=> ( 
			<h1> number <h1/> 
		))} 
	</> 
)
```


<details>
  <summary>SPOILER!</summary>
  
```
export  const getStaticProps = async () => { 
	const res = await fetch(`${process.env.API_URL}?api_key=${process.env.API_KEY}`); 
	const moviesAll = await res.json(); 
	const movies = moviesAll.results; 
	return { 
		props: { 
			movies, 
			} 
		} 
	}
```

</details>

