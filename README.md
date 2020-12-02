# Technical Test: Tech lead

Write a React Hook to streamline data fetching from API, that can be used this way:

## Code example

```javascript
import UseFetch, { useFetch } from '@TechnicalTest/useFetch'

// DSL Configuration
UseFetch.configure((config) => {
  config.baseUrl = 'http://localhost:3001/api/v1/'
  config.authentificationHeader = () => {
    return {
      'Authorization': localStorage.getItem('id_token')
    }
  }
  config.compressionTimeoutDelay = 200
  config.maximumSize = 2 * 1024 * 1024 // 2Mb.
  config.protectedFromCleaning = ['profile', /^patients\/importants/]
})

// Usage on application component
const Patients = () => {
  const [{ patients, cancerOrigins }, isLoading] = useFetch((fetch) => ({
    patients: fetch('patients/active'),
    cancerOrigins: fetch('cancer_origins', { queryParams: { order: 'label ASC' }})
  }))

  return (
    <Fragment>
      {isLoading && <MyFancyLoader />}
      <FilterByCancerOrigin />
      <PatientList />
    </Fragment>
  )
}

const FilterByCancerOrigin = () => {
  // Fetched data can be read from child components
  const cancerOrigins = useSelector(({ cancerOrigins }) => cancerOrigins)

  return (
    <select>
      {cancerOrigins.map((origin) => <option value={origin.id}>{origin.label}</option>)}
    </select>
  )
}
```

## Requirements:

- The hook should store fetched data in a global Redux store and manage gracefully the case where no Provider is defined.
- The hook should use `fetch` (and not `axios`) and allows to pass a baseUrl (prefix) of the API so it won't be required for each request.
- Data should also be stored in localstorage for cache purpose. When page is reloaded, data should be read instantly from localStorage while making the request in the background to update the cache.
- Compress data before storing it in localstorage using frontend compression technology (eg. LZCompress)
- When localStorage is out of space, delete the first item stored in localStorage that is bigger than the space needed to store the new item.
- Add a way to protect certain entries from being cleaned.


## Bonus to impress us!

- When compression takes more than 200milliseconds, abort it and store uncompressed data. The script should know how to recognize compressed and no compressed data at the moment of reading it from cache.
- Allow nested (multi-level) usage of the `useFetch` while using the same root Redux store.


## Deliverable:

- Code should be delivered as a working Web application that demonstrates all the requirements (open-bar for the content).
- Code should be delivered as a Git repository having multiple incremental commits and not a single big commit.
- Will be judged: the quality of the code, the quality of commits, the quality of the documentation (in and outside of code), capacity in asking for help and the elegance of the solution.
- The point is not for you to figure things out from scracth if you have never done something similar. Feel free to ask for pointers and help when stuck.
- Bonus (not needed): deployed version of the app.


## Notes:

- This is a pure React front-end technical test, no need to build an API. You can use public data API.
