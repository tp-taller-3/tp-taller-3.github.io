# GraphQL y Apollo

[GraphQL](https://graphql.org/learn/)
es la principal interfaz entre el backend y el frontend. Para la subida del archivo, se usó REST http clásico.

## Apollo Server (back-end)

El backend utiliza la librería
[Apollo Server](https://www.apollographql.com/docs/apollo-server/)
para declarar el mapping, compuesto por types (como `Semester`),
mutations (como `deleteSemester`) y queries (como `getSemesterByUuid`).
Expone el único endpoint `/graphql`.

Tanto los types como las queries y las mutations se declaran con una función
[resolver](https://www.apollographql.com/docs/apollo-server/data/resolvers/)
(por ejemplo, `resolve` en `GraphQLAdmin -> fields -> user`).

Si no se especifica, se obtiene el atributo con el mismo nombre del field
(por ejemplo, para resolver `GraphQLAdmin -> fields -> secretary`
no dar función `resolve` equivale a `resolve: admin => admin.secretary`).

Para el manejo de permisos se usa
[GraphQL Shield](https://github.com/maticzav/graphql-shield/blob/main/README.md): en el archivo `permissionShield.ts` se declaran los permisos
necesarios para acceder a
una query, mutation o un field de un type. Si no se declara, es público.

Por ejemplo, en `adminPermissions`: `Query.getAdmins` solo es
accesible por admins.

## Apollo Client (front-end)

El frontend utiliza la librería
[Apollo Client](https://www.apollographql.com/docs/react/)
para ejecutar queries y mutations, cuyo resultado se almacena en la caché de Apollo.

Las queries y mutations están escritas en archivos `.graphql`,
y son utilizadas por medio de
[hooks de react](https://reactjs.org/docs/hooks-overview.html)
propios, que utilizan los
[hooks que brinda Apollo Client](https://www.apollographql.com/docs/react/api/react/hooks/).

Por ejemplo, el componente `DeleteSemesterActionContainer` llama al hook
`useSemesterByUuid` con el parámetro `uuid` obtenido de la url.
Dicho hook delega el trabajo al hook `useQuery`,
dándole el archivo `.graphql` y settings correspondientes.
Dicho hook da una respuesta del tipo `{ data, loading, error }` al componente.

Para evitar repetir queries innecesariamente,
los resultados se guardan en la caché.
Se puede especificar en
[la setting `fetchPolicy`](https://www.apollographql.com/docs/react/data/queries/#setting-a-fetch-policy)
cuándo se la quiere usar. **Se utiliza por default.**

En la carpeta `src/cache` se declaran las settings de la caché.
En `InMemoryCache.ts` se declara que el atributo `uuid` (o en su defecto `id`)
se utiliza para identificar al objeto en la caché (es decir, es su `dataID`).

**Es necesario** para utilizar la caché tener un **`dataID` declarado, y pedirlo al
ejecutar una query** o mutation. Si no, no se puede identificar el objeto.
Es recomendable explorar y entender el contenido de la carpeta
`src/cache`, guiándose con la
[documentación de la caché de Apollo](https://www.apollographql.com/docs/react/caching/cache-configuration/).

También es recomendable usar la extensión del navegador
[Apollo Client Developer Tools](https://chrome.google.com/webstore/detail/apollo-client-developer-t/jdkknkkbebbapilgoeccciglkfbmbnfm).
