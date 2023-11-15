
# Rails-avanzado

#### Vistas parciales, validaciones y filtros
##Renders 
Los partials nos ayudan a refactorizar nuestro codigo y evitar repetirnos

Este será el codigo que será reemplazado por el partial

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/cd321816-6534-4f1a-8ebc-30c6e99d0b19)

Reemplazando con el partial queda así 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/0f22eec6-cf95-4e19-b70a-80445715104d)

Siendo el partial 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/52b267ad-b214-425f-b5ab-584b2bba1cb5)

Tambien podría estar en otra ubicación solo tendríamos que cambiar la dirección, por ejemplo en partial: 'shared/movie' 
Además si utilizamos la extensión .haml funciona exactamente igual 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/1d8dbf7a-f13a-49d5-ac74-ce936354cd64)

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/06efb5a4-1e1a-4370-aab2-9eb73e40d64d)

Solo que al usar varios div la ubicación de los elementos se presentan en una sola columna



Modificación clase movie
Las validaciones agregadas en el codigo nos ayudan a evitar errores como crear objetos mal definidos
![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/896aaa30-c5a8-4dbc-b73c-055c08baf50a)
Comprobando los cambios realizados 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/bcc14432-d738-49ee-8022-e3b39580cb3c)

Explicación de código

```
Las distintas funciones definidas en el controlador nos permitirá mediante routes.rb usar cada función, por ejemplo 
get "movies/new", to: "movie#new"
así es como para la ruta "movies/new" usamos la función new del controlador Moviecontroller, finalemente con se mostrará
la vista new.html.haml cuando se acceda a la ruta correspondiente
class MoviesController < ApplicationController
  def new #se define una función que crea un objeto Movie con valores nil en todos sus campos
    @movie = Movie.new 
  end 
  def create  #se intenta crear un objeto movie con los parametros  title, rating, release_date
    if (@movie = Movie.create(movie_params))
      redirect_to movies_path, :notice => "#{@movie.title} created."
    # si es exitoso redirecciona al menu principal
    # y cambia el flash de tipo notice, informando de la creación de la pelicula
    else #caso contrario muestra un alert en la página informando el error separadas por una coma, luego renderiza la misma página
      flash[:alert] = "Movie #{@movie.title} could not be created: " +
        @movie.errors.full_messages.join(",")
      render 'new'
    end
  end
  def edit #en la ruta "movie/:id/edit" se busca una pelicula con ese id para luego en la vista mostrarlo
    @movie = Movie.find params[:id]
  end
  def update # de igual manera busca una película con su id y si es exitoso la actualización lo muestra en el flash
    @movie = Movie.find params[:id]
    if (@movie.update_attributes(movie_params))
      redirect_to movie_path(@movie), :notice => "#{@movie.title} updated."
    else #sino muestra el error separado por coma y renderiza la misma vista "edit"
      flash[:alert] = "#{@movie.title} could not be updated: " +
        @movie.errors.full_messages.join(",")
      render 'edit'
    end
  end
  def destroy # en la ruta delete "movie/:id" se busca una película y se intenta borrar de la base
    @movie = Movie.find(params[:id])
    @movie.destroy #lo elimina y nos dirige al menú principal de todas las peliculas y notifica de la película eliminada
    redirect_to movies_path, :notice => "#{@movie.title} deleted."
  end
  private
  def movie_params #restringe los parametros del objeto movie para que el objeto sea creado con ciertos parametros
    params.require(:movie)
    params[:movie].permit(:title,:rating,:release_date)
  end
end
```
Mecanismo para canonicalizar

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/762b0288-0af3-4443-907f-c01683726141)

Prueba de consola

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/cfca0285-d15f-4de3-92aa-4ea56c4558a0)

####SSO y autenticación a través de terceros

Se crea el modelo, se realiza la migración y se edita el modelo
Creacion de la tabla

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/36987270-c4fd-4c67-8280-828d2eebfa39)

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/a8882b59-041c-45cc-8438-57710610af49)

Se instala la gema y se crea el controlador sessions

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/39af82b6-4ecd-4fc7-9958-98f27db704f9)

Se busca la API KEY Y SECRET KEY 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/73b1e34f-2547-4251-bfde-eb910610131a)


![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/62a97607-f363-4b19-b700-82c98b1fd466)

Pregunta: Debes tener cuidado para evitar crear una vulnerabilidad de seguridad. ¿Qué sucede si un atacante malintencionado crea un envío de formulario que intenta modificar params[:moviegoer][:uid] o params[:moviegoer][:provider] (campos que solo deben modificarse mediante la lógica de autenticación) publicando campos de formulario ocultos denominados params[moviegoer][uid] y así sucesivamente?.

El problema se le llama asignación masiva y es posible si no delimitamos los valores permitidos por el
formulario, para esto utilizamos params.require(:moviegoer).permit(:provider, :uid, :name) y así evitaríamos ese tipo de problemas

####Asociaciones y claves foráneas
Explica la siguientes líneas de SQL:

SELECT reviews.*
    FROM movies JOIN reviews ON movies.id=reviews.movie_id
    WHERE movies.id = 41;

esta consulta en lenguaje SQL nos permite buscamos mostrar todas las filas de la tabla reviews, donde el codigo de las peliculas
son igual al codigo de las peliculas en la tabla reviews, donde el codigo de la pelicula sea 41

Para este ejemplo primero se creará la pelicula y las personas
![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/e06c3639-f60f-452b-a548-36230ba29190)

Obtenemos sus ids

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/fc6933d5-02ef-428c-aba9-90dc3e8aebf3)

Asignamos calificaciones 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/0d623e16-a75f-4fe9-9c11-7e5cfa7f39ae)

Asignamos un array de las calificaciones de la película 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/ec8aaead-3ff1-4777-9bdd-1ee34eef3c98)

Además a como cada critico tiene varias críticas se guarda en un array alice_reviews

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/3296ac99-0ae6-4622-bd78-fdb9440abd6a)

Finalmente se visualiza quien ha criticado la película

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/47242918-39e4-4941-892e-5c3ad0236c1a)

Uso de validaciones

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/323dabf2-c4a2-4382-bdd7-ea433a9c6ae4)

Se pueden utilizar validates_associated :movie para que al querer crear una reseña se valide que sea correcta
según sus propias validaciones, mientras que, validates :movie_id , :presence => true, sirve para validar que 
movie_id no sea nula, osea que exista una película


Como llamar a save o save! sobre un objeto que usa asociaciones también afecta a los objetos a los que esté asociado, se aplican algunas salvedades si alguno de estos métodos falla. Por ejemplo, si acabas de crear una nueva Movie y dos nuevas Review que asociar a esa Movie`, e intenta guardar dicha película, cualquiera de los tres métodos save que se apliquen fallarán si los objetos no son válidos (entre otras razones). !Comprueba esto!.

Para comprobar esto haremos el siguiente cambio

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/d62cac54-228d-4944-ad10-110dca378a75)

####Ahora en la consola haremos un experimento
Veremos como las asociaciones también afecta a los objetos a los que esté asociado
Se crea una película

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/249ba482-6399-4eb6-851d-b3d9f7025832)

Dos reseñas

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/8a4a724d-1796-4fae-a789-6cc0ec80796a)

Intentamos guardar 

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/b2f4cfeb-5b19-4d55-b531-221397296e2a)

El error que nos arroja significa que las reseñas por sí mismas están mal, por eso no se pueden añadir a nuestra lista de reseñas

####Destroy 
En este experimento se podrá ver como si eliminamos una película, las reseñas de esa película van a desaparecer también 

Se crea críticos, un película, se agrega las reseñas de alice y bob a las reseñas de la película

![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/3b1e4c2e-ef03-43f1-ae52-7f6349ce9e1d)

Vemos cuantas reseñas hay antes de borrar la película y cuantas hay luego de borrarla
![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/6f57886b-5f1c-40a8-aee7-69ab131859ec)
![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/5a4dd047-00d6-4899-9281-ded90062dcf7)
![imagen](https://github.com/AltherEgo/Rails-avanzado/assets/119552157/8c1a6528-f53d-43cb-b496-d860cea842fd)

Se muestra como has_many :reviews,:dependent=>:destroy funciona 

