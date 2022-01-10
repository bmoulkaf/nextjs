# Welcome to NextJs TP
## Introduction
vous venez d'avoir une presentation sur NextJs, avec une demo et un aperçu 
## Prerequis

pour creer le projet: 
`npx create-next-app nextjs-movies`

pour lancer le serveur :
`cd nextjs-movies`

`npm run dev`
cette commande execute le script qu'on peut trouver dans:
`nextjs-movies/package.json -> scripts -> dev`

## Styling
Si on fait une site web, ca doit quand meme etre un site stylé, pour ca on va utilise un ...

`npm install antd --save`

Manually import the **antd.css** file at the very top of your **pages/_app.js** file:

`import  'antd/dist/antd.css';`


## Structure du projet
a page is a React Component exported from a file in the [`pages`  directory](https://nextjs.org/docs/basic-features/pages)
keep in mind, every component can return only one <div>... (a changer)

### Premier composant

Pour commencer on va creer notre composant card qui va representer un film dans notre catalogue, 
create folder components in which we will store all our components,
inside we create a folder for each component, name it Card
and inside we create index.js
this is good practice to have every component with its css in the same folder, 
a bit less useful to us since we're using antd
we only have to specify the name of the folder in the import

On va ensuite utiliser un composant Card de antdesign:
https://ant.design/components/card/
Vous pouvez choisir celui que vous voulez, puis copier le code en clickant l'icone juste en dessous du modele.
ensuite on va devoir changer le `ReactDOM.render`, et utiliser un export de fonction a la place, par exemple:
```js
import { Card } from  'antd';
const { Meta } = Card;
ReactDOM.render(
[...],
mountNode,
);
```
deviendra:
```js
import { Card } from  'antd';
const { Meta } = Card;
export  default  function  Card(){
	return(
		[...]
	)
};
```

### main page

notre page principal sera donc notre catalogue, qui devra donc etre organisé.
Pour ca, on va retourner voir notre cher ami antd, et prendre ce qui va constituer notre layout.
Pour l'exemple, on va utiliser le layout [grid](https://ant.design/components/grid/) , mais vous pouvez encore une fois choisir le layout que vous voulez.
on va alors dans `pages/index.js`, et on met layour qu'on a choisi:
```js
import { Row } from  'antd';
export  default  function  Home() {
	return (
	<>
		<Row  justify="space-around"  align="middle"  gutter={[20, 20]}>
		</Row>
	</>
	)
}
```

on importe ensuite notre composant `Card`,
qu'on peut invoquer comme ceci: `<Card />`

#### Fetch Data
on va maintenant recuperer les données de films, pour ca on va consommer l'api qu'on a defini dans le `.env`.
On fetch les données dans notre fonction `getStaticProps`,qu'on oublie pas d'exporter aussi, puis on les retourne dans un object js dans un champ `props`.
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


### Create components from the data
On va ensuite boucler sur notre liste de films, et créer un composant pour chaque element de la liste, en lui passant les proprietes:
 - `movie`: qui va etre l'objet de la liste.
 - `key`: qui doit etre un identifiant unique  
 
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
  
```js
export  default  function  Home(movies) {
	return (
		<>
			<Row  justify="space-around"  align="middle"  gutter={[20, 20]}>
				{movies.map(movie  => (
					<Card  movie= {movie}  key={movie.id}  />
				))}
			</Row>
		</>
	)
}
```

</details>

