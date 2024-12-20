<h1 align="center">🧙‍♂️ Custom Hooks 🧙‍♂️</h1>

A custom hook is simply a normal JS <code>function</code> whose purpose is to wrap all the <code>state/logic</code> which is closely-related and repetitive to use it wherever you want avoiding that boilerplate code and encouraging reusability. 

In this project, i used a <code>custom hook</code> to group up a <code>fetch</code> feature that manages 3 related <code>states</code> and some <code>logic</code>

```javascript
  //states
  const [userPlaces, setUserPlaces] = useState([]);
  const [isFetching, setIsFetching] = useState(false);
  const [error, setError] = useState();
  //logic
  useEffect(() => {
    async function fetchPlaces() {
      setIsFetching(true);
      try {
        const places = await fetchUserPlaces();
        setUserPlaces(places);
      } catch (error) {
        setError({ message: error.message || 'Failed to fetch user places.' });
      }
      setIsFetching(false);
    }
    fetchPlaces();
  }, []);
```

## Creating the <code>Custom Hook</code> 🔧

I organized the project by adding a <code>hooks</code> folder and a <code>useFetch.js</code> file. 

- <code>folder</code> ➡️ can also be named <code>customhooks</code> or anything descriptive.
- <code>file</code> ➡️ name must start with <code>use</code> to comply and take advantage of <code>React Hooks Rules</code>

```javascript
export function useFetch(fetchFn,initialValue){
  //modified states
  const [isFetching, setIsFetching] = useState(false);
  const [data, setData] = useState(initialValue);
  const [error, setError] = useState();
  //modified logic
  useEffect(() => {
    async function fetchData() {
      setIsFetching(true);
      try {
        const data = await fetchFn();
        setData(places);
      } catch (error) {
        setError({ message: error.message || 'Failed to fetch data.' });
      }
      fetchData(false);
    }
    fetchPlaces();
  }, [fetchFn]);

  return {
  isFetching,
  data,
  setData,
  error
}
```

- Generalize <code>state/logic</code> ➡️ state and logic variables names are now more generic.
- Flexible <code>Parameters</code> ➡️ added <code>fetchFn</code> and <code>initialValue</code> to allow dynamic usage.
- Effect Dependencies ➡️ ensured <code>fetchFn</code> is listed as a dependency for proper reusability.
- Reusability ➡️ returns an <code>object</code> containing all <code>values</code> and <code>functions</code> to expose.

## Using the <code>Custom Hook</code> 🔄

Now, this <code>custom hook</code> can be used as follows:

```javascript
  import { useFetch } from "./hooks/useFetch.js";
  const { isFetching, data: userPlaces, error } = useFetch(fetchUserPlaces, []);
  ...
  <DummyComponent isLoading={isFetching} places={userPlaces} />
  {error && <Error title="An error occurred!" message={error.message} />}
```

## Another Use Case: Nested Logic in <code>Fetch</code> 🎩
In <code>AvailablePlaces.jsx</code>, I needed to <code>fetch</code> available places and sort them by user location using the <code>navigator</code> API:

```javascript
 useEffect(() => {
          ...
                navigator.geolocation.getCurrentPosition((position) => {
                const sortedPlaces = sortPlacesByDistance(
                  places,
                  position.coords.latitude,
                  position.coords.longitude
                );
          ...
    }
```

To integrate this with the <code>custom hook</code>, a <code>customized fetch function</code> was needed:
```javascript
  //create a function with all the nested behavior what is needed
   async function fetchSortedPlaces(){
   const places = await fetchAvailablePlaces() // first retrieve all that places
   //then returns a Promise with the resolve (sortedPlaces) or reject (not handled in this case)
   return new Promise((resolve,reject) => {
   navigator.geolocation.getCurrentPosition((position) => {
                const sortedPlaces = sortPlacesByDistance(
                  places,
                  position.coords.latitude,
                  position.coords.longitude
                );
                resolve(sortedPlaces)
   })
   })
   }
```
Now, use the <code>custom hook</code> with the <code>fetch</code> function

 <code> const { isFetching, data: availablePlaces, error } = useFetch(fetchSortedPlaces, []); </code>
 
## Quick Recap 🔄

- Create a reusable <code>custom hook</code> file (<code>useFn.js</code>) to manage some closely-related <code>state/logic</code>.
- Generalize the <code>state/logic</code> and also add <code>parameters</code> for flexibility.
- Handle other use cases with <code>customized functions</code> (<code>async/Promise</code>).

Finally, the project is cleaner and the <code>custom hook</code> can be easily reused across <code>components</code> as it is unique to each component's use.


---
<p align="center">🐸 This project is a practice exercise I learned from the <a href='https://www.udemy.com/course/react-the-complete-guide-incl-redux/?couponCode=ST7MT110524'>Academind's React Course</a> 🐸</p>

