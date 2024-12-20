<h1 align="center">🧙‍♂️ Custom Hooks 🧙‍♂️</h1>

A custom hook is just a normal javascript function which purpose is to contain all the state logic which is closely-related or repetitive on a few components to use it wherever you want avoiding that boilerplate code. In this project, i use a custom hook to group up a fetch feature which manage these 3 states.

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

So, i created a hooks folder and then added a useFetch.js file
- hooks folder can also be named customhooks or anyname you think is good
- 'use' word before Fetch.js must be necessary to leverage and follow all the React Hooks Rules
Now, all the body function must be modified to encourage the reusability and making it a proper custom hook

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

- state naming and logic all more general named
- fetchFn and initialValue added to enhance a more flexible custom hook
- fetchFn must be a useEffect's dependency since it will change when it is used in other components
- must return a object or array (or some other expression) which contains all the values or fn i want to expose

With all that changes now i use that custom hook as is intended

  import { useFetch } from "./hooks/useFetch.js";
  const { isFetching, data: userPlaces, error } = useFetch(fetchUserPlaces, []);
  ...
  <DummyComponent isLoading={isFetching} places={userPlaces} />
  {error && <Error title="An error occurred!" message={error.message} />}

In this project, i have some nested logic in my fetch feature on AvailablePlaces.jsx as you can see below:
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

Basically i want to fetch all the available places and then sorted them by the user location using that built-in navigator API.
In the custom hook code there is a const data = await fetchFn(); which means it will wait a Promise that need to be resolved or not
That means i'll need to wrap all the sorted places in a Promise since that is what im awaiting for in my custom hook in this case

  //create a function with all the nested behavior it is needed
   async function fetchSortedPlaces(){
   const places = await fetchAvailablePlaces() // first retrieve all that places
   //then return the Promise with the resolve or reject (not handleded in this case)
   return new Promise((resolve,reject) => {
   navigator.geolocation.getCurrentPosition((position) => {
                const sortedPlaces = sortPlacesByDistance(
                  places,
                  position.coords.latitude,
                  position.coords.longitude
                );
                resolve(sortedPlaces) //pass sorted places as the resolve result
   })
   })
   }

   Now, this customized function could be passed to the custom hook

   const { isFetching, data: availablePlaces, error } = useFetch(fetchSortedPlaces, []);



---
<p align="center">🐸 This project is a practice exercise I learned from the <a href='https://www.udemy.com/course/react-the-complete-guide-incl-redux/?couponCode=ST7MT110524'>Academind's React Course</a> 🐸</p>

