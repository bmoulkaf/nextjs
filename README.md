# nextjs

#### Fetch Data
on va maintenant recuperer les données de films, pour ca on va consommer l'api qu'on a defini dans le `.env`.
On fetch les données dans notre fonction `getStaticProps`,qu'on oublie pas d'exporter aussi, puis on les retourne dans un object js dans un champ `props`.

### Create components from the data
On va ensuite boucler sur notre liste de films, et créer un composant pour chaque element de la liste.
**Tip:** 
Pour executer du code JS a l'interieur du code HTML, on utilise des accolades:


<details>
<summary>I could use some help...</summary>
<p>

```c#
public class Order
{
    public int OrderId { get; set; }
    public int CustomerId { get; set; }

    public List<int> Products { get; set; }
}
```

</p>
</details>  


```js
return ( 
	<> 
		{['1','2'].map(number=> ( 
			<h1> number <h1/> 
		))} 
	</> 
)
```
