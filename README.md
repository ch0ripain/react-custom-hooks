<h1 align="center">🧙‍♂️ Custom Hooks 🧙‍♂️</h1>

A custom hook is simply a normal javascript function whose purpose is to wrap all the state logic which is closely-related and repetitive to use it wherever you want avoiding that boilerplate code and encouraging reusability. 

In this project, i used a custom hook to group up a fetch feature that manages 3 related states and some logic.

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

## Creating the Custom Hook

I organized the project by adding a hooks folder and a useFetch.js file. 

- The folder can be named customhooks or anything descriptive.
- The file name must start with use to comply and take advantage of React Hooks Rules.

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

- Generalized State and Logic: State names and logic are now more generic.
- Flexible Parameters: Added fetchFn and initialValue to allow dynamic usage.
- Effect Dependencies: Ensured fetchFn is listed as a dependency for proper reusability.
- Reusability: Returns an object containing all values and functions to expose.

## Using the Custom Hook

Now, this custom hook can be used as follows:

```javascript
  import { useFetch } from "./hooks/useFetch.js";
  const { isFetching, data: userPlaces, error } = useFetch(fetchUserPlaces, []);
  ...
  <DummyComponent isLoading={isFetching} places={userPlaces} />
  {error && <Error title="An error occurred!" message={error.message} />}
```

## Another Use Case: Nested Logic in Fetch
In AvailablePlaces.jsx, I needed to fetch available places and sort them by user location using the navigator API:

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

To integrate this with the custom hook, a custom fetch function is needed:
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
Now, use the Custom Hook with the Fetch Function:
 const { isFetching, data: availablePlaces, error } = useFetch(fetchSortedPlaces, []);
 
## Quick Recap 🔄

- Create a reusable custom hook file (useFn.js) to manage some closely-related state/logic.
- Generalize the state/logic and also add paramethers for flexibility.
- Handle other use cases with customized functions (async/Promise).

Finally, the project is cleaner and the custom hook can be easily reused across components as it is unique to each component's use.


---
<p align="center">🐸 This project is a practice exercise I learned from the <a href='https://www.udemy.com/course/react-the-complete-guide-incl-redux/?couponCode=ST7MT110524'>Academind's React Course</a> 🐸</p>

