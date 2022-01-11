# TP NextJS - DSFFS

## Introduction
Dans ce TP, vous allez développer à partir de zéro une petite application qui vous permettra de d'afficher un catalogue de films et de consulter les informations disponibles à leur sujet. L'application récupèrera les données nécessaires via l'API de [The Movie Database](https://www.themoviedb.org/?language=fr).

Quelques ressources utiles :
- L'application de la demo : https://nextjs-demo-96t0oc27b-brahim-lqati.vercel.app/
- La présentation de la démo : https://presentation-nextjs-qckhc8jxx-brahim-lqati.vercel.app/

## Prérequis

Assurez-vous d'avoir installé Node.js, version 10.13 ou ultérieure. Vous pouvez télécharger Node [à cette adresse](https://nodejs.org/en/).

Pour créer un nouveau projet, exécutez:

`npx create-next-app nextjs-movies`

Puis lancez le serveur:

`cd nextjs-movies`

`npm run dev`

(Pour les curieux, la commande `dev` provient de
`nextjs-movies/package.json -> scripts -> dev`)

Vous devriez à présent voir l'URL vous permettant d'accéder à l'application s'afficher dans votre terminal.

Pour terminer la configuration initiale, créez un fichier `.env` à la racine du projet. Ceci va nous permettre de configurer les variables d'environnement dont nous aurons besoin pour utiliser l'API de TMDB. Copiez-y les lignes suivantes:

`API_KEY = b9c543de689f5cf5638b93f066de05b5`

`API_URL = https://api.themoviedb.org/3/movie`

## Styles
Pour ajouter facilement des éléments d'interface qui en jettent à notre application, nous allons utiliser la librairie Ant Design. Ant Design met à notre disposition une multitude de composants pré-stylés.

`npm install antd --save`

Pour pouvoir utiliser Ant Design, importez **antd.css** dans le fichier **pages/_app.js** de l'application, comme ceci :

`import  'antd/dist/antd.css';`

## Structure du projet

Chaque page de l'application est constituée d'un composant React, exporté depuis un fichier contenu dans le dossier [`pages`](https://nextjs.org/docs/basic-features/pages). On écrit un composant sous forme de fonction qui renvoie le contenu à afficher.

Attention, un composant ne peut avoir qu'une seul balise HTML à sa racine.
Ainsi cette structure de composant est valide :

```html
export default function Home() {
	return (
		<div>
			Premier div
			<div>
				Deuxième div
			</div>
		</div>
	)
}
```

Mais pas celle-là :

```html
export default function Home() {
	return (
		<div>
			Premier div
		</div>

		<div>
			Deuxième div
		</div>
	)
}
```

Ce mélange de Javascript et de HTML utilisé par React est appellé [JSX](https://fr.reactjs.org/docs/introducing-jsx.html).

## Premier composant

Il est temps de créer notre premier composant, MovieCard. Ce composant nous permettra d'afficher un film dans le catalogue, avec son affiche et ses informations.

Avant tout, créez un dossier `components` à la racine du projet, dans lequel seront rangés les composants.

Une bonne pratique pour garder ses projets organisés est de garder chaque composant avec tous ses fichiers (tests, CSS...) dans un dossier séparé. Créez le dossier `components/MovieCard` qui contiendra le composant MovieCard, et créez-y un fichier `MovieCard.js`.

Bonne nouvelle, la librairie Ant Design que nous avons installée fournit déjà des composants qui font exactement ce dont on a besoin, dont un composant Card que l'on peut réutiliser. La page https://ant.design/components/card/ indique comment utiliser ce composant son projet. Choisissez l'un des exemples, puis copiez le code en-dessous dans `MovieCard.js`.

Votre composant devrait ressembler à ceci:

```js
import { Card } from  'antd';
const { Meta } = Card;

ReactDOM.render(
	[...],
	mountNode,
);
```

Remplacez ensuite `ReactDOM.render` par un export de fonction. Cela nous permettra d'utiliser notre composant MovieCard au sein des composants que nous créerons par la suite, en l'important là nous nous en aurons besoin.

```js
import { Card } from  'antd';
const { Meta } = Card;

export  default  function  MovieCard(){
	return(
		[...]
	)
};
```

## Page principale

La page principale affichera notre catalogue, c'est-à-dire une liste de films qui seront chacun affichés via une instance de notre composant MovieCard. Pour structurer un peu le catalogue, nous allons utiliser l'un des layouts fournis par Ant Design.

Pour l'exemple, nous allons utiliser le layout [grid](https://ant.design/components/grid/), mais vous pouvez encore une fois choisir le layout que vous voulez.
Intégrez le layout choisi dans le composant Home de `pages/index.js` (vous pouvez supprimer le contenu généré par Next.js lors de la création de l'application) :

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

En haut du fichier, on importe ensuite notre composant `MovieCard`.
Par la suite pour l'insérer au sein de notre page on pourra l'invoquer comme ceci: `<MovieCard />`.

### Récupérer les données

On va maintenant récuperer les données des films dont nous avons besoin pour peupler notre page. Pour cela, nous allons consommer l'API que nous avons précédemment définie dans le fichier `.env`. Pour mieux comprendre l'API, vous pouvez consulter sa documentation: https://developers.themoviedb.org/3/movies/get-upcoming.

On récupère les données dans une fonction appellée `getStaticProps`, qu'il faut ajouter à `index.js` et ne pas oublier d'exporter. Une fois les données obtenues, on les retourne dans un object contenu dans un champ `props`, qui sera passé au composant Home.

<details>
  <summary>Solution</summary>
  
```js
export  const getStaticProps = async () => { 
	const res = await fetch(`${process.env.API_URL}/upcoming?api_key=${process.env.API_KEY}`); 
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

## Créer des composants à partir des données

Au sein de notre composant Home, nous allons ensuite itérer sur notre liste de films, et créer un composant MovieCard pour chaque élement de la liste. Lorsque l'on crée un composant MovieCard, on veut également lui passer les propriétés suivantes:
 - `movie`: l'objet récupéré dans la liste, contenant les données du film.
 - `key`: un identifiant unique pour chaque objet de la liste, comme son ID. Il est requis par React pour des raisons de [performance](https://fr.reactjs.org/docs/reconciliation.html#recursing-on-children).
 
**Tip:** 
Pour évaluer du code Javascript a l'interieur du code JSX, on utilise des accolades, par exemple:
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
  <summary>Solution</summary>
  
```js
export  default  function  Home({movies}) {
	return (
		<>
			<Row  justify="space-around"  align="middle"  gutter={[20, 20]}>
				{movies.map(movie  => (
					<MovieCard  movie= {movie}  key={movie.id}  />
				))}
			</Row>
		</>
	)
}
```

</details>

Il reste à modifier le composant `MovieCard` pour qu'il affiche correctement les informations du film qui lui est passé en parametre.
On veut présenter:

 - La photo de l'affiche du film
 - Le titre du film
 - La note moyenne donnée au film
 - Un indicateur de popularité du film

Pour finir, on veut rendre `MovieCard` cliquable. Cliquer dessus nous mènera vers une page donnant des détails supplémentaires sur le film sélectionné.
Pour cela on utilise le composant `Link` de Next.js, qui permet de naviguer entre différentes pages :

`import Link from 'next/link';`

Pour rendre `MovieCard` cliquable, on encapsule notre composant `MovieCard` à l'intérieur d'un composant `Link`. Vous pouvez laisser l'adresse de destination vide, puisque nous n'avons pas encore de pages dédiées aux films vers lesquelles rediriger l'utilisateur.


**Tip:**
Pour trouver l'adresse à laquelle récupérer l'image du film, voir cette [documentation](https://developers.themoviedb.org/3/getting-started/images).
 <details>
  <summary>Solution</summary>
  
```js
export default function MovieCard({movie}) {
    return (
		<Link href={`/movies/${movie.id}`}>
			<Card
				style={{ width: 300 }}
				cover={
					<img
						alt="cover movie"
						src={`https://image.tmdb.org/t/p/w500/${movie.poster_path}`}
					/>
				}
				actions={[
					<p>
						{movie.popularity}
					</p>,
					<p>{movie.vote_average}</p>,
				]}
			>
			<Meta
				title={movie.title}
			/>
			</Card>
		</Link>
	);
}
```

</details>


## Page dédiée pour chaque film
Il faut maintenant créer une page pour chaque film. Next.js peut nous aider à réaliser cela grâce à une route dynamique, de la forme /movies/[idDuFilm]. Comme cité dans le cours, Next.JS peut générer ces pages HTML directement lors de la compilation du site (en mode développement, le site est recompilé à chaque modification).
Pour cela, il faudra utiliser :
- Une page `pages/movies/[id].js`.
- La méthode `getStaticPaths` pour récupérer les ids des films, et à partir de cela retourner la liste de tous les paths de nos pages à générer.
- Un composant MoviePage qui permettra d'afficher les informations d'un film donné.
- La méthode `getStaticProps` qui va prendre en paramètre un id de film, pour récupérer davantage de détails sur le film correspondant et les fournir au composant MoviePage qui les affichera. Voir la [documentation](https://developers.themoviedb.org/3/movies/get-movie-details) de l'API.

 <details>
  <summary>Solution</summary>
  
```js
export default function MoviePage( {movie} ) {
    // A vous de voir comment styler ce composant...
    return(
        <div>
            <h1>{movie.title}</h1>
        </div>
    )
}

export const getStaticPaths = async () => {
    const res = await fetch(`${process.env.API_URL}/upcoming?api_key=${process.env.API_KEY}`)
    const moviesAll = await res.json();
    const movies = moviesAll.results;

    const ids = movies.map(movie => movie.id)
    const paths = ids.map(id => ({ params: { id: id.toString() } }))
    return {
        paths,
        fallback: false
    };
}

export const getStaticProps = async (context) => {
    const id = context.params.id;
    const res = await fetch(`${process.env.API_URL}/${id}?api_key=${process.env.API_KEY}`)
    const movie = await res.json();

    return {
        props: {
            movie,
        },
    }
}
```

</details>

## Bonus

Vous pouvez utiliser Vercel pour déployer très facilement votre application, simplement à partir d'un dépôt GitHub : https://vercel.com/solutions/nextjs
