## Debounce in React

##### Debounce in Vanilla Javascript

[Debounce in Vanilla Javascript](https://youtu.be/tYx6pXdvt1s)

##### useMemo

[useMemo Hook](https://youtu.be/R49sY--qOqA)

#### Initial Setup

```js
import React, { useState } from 'react';
import { useGlobalContext } from '../context';
export default function SearchForm() {
  const [searchTerm, setSearchTerm] = useState('');
  const { fetchDrinks } = useGlobalContext();

  const handleSubmit = (e) => {
    e.preventDefault();
  };

  const searchCocktail = (e) => {
    const searchTerm = e.target.value;
    setSearchTerm(searchTerm);
    fetchDrinks(searchTerm);
  };

  return (
    <section className='section search'>
      <form className='search-form' onSubmit={handleSubmit}>
        <div className='form-control'>
          <label htmlFor='name'>search your favorite cocktail</label>
          <input
            type='text'
            name='name'
            id='name'
            onChange={searchCocktail}
            value={searchTerm}
          />
        </div>
      </form>
    </section>
  );
}
```

#### Without Debounce

```js
import React, { useState } from 'react';
import { useGlobalContext } from '../context';
export default function SearchForm() {
  const [searchTerm, setSearchTerm] = useState('');
  const { fetchDrinks } = useGlobalContext();

  const handleSubmit = (e) => {
    e.preventDefault();
  };

  const searchCocktail = () => {
    let timeoutId;
    return (e) => {
      const searchTerm = e.target.value;
      setSearchTerm(searchTerm);
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        fetchDrinks(searchTerm);
      }, 1000);
    };
  };

  return (
    <section className='section search'>
      <form className='search-form' onSubmit={handleSubmit}>
        <div className='form-control'>
          <label htmlFor='name'>search your favorite cocktail</label>
          <input
            type='text'
            name='name'
            id='name'
            onChange={searchCocktail()}
            value={searchTerm}
          />
        </div>
      </form>
    </section>
  );
}
```

#### Debounce With useMemo

```js
import React, { useMemo, useState } from 'react';
import { useGlobalContext } from '../context';
export default function SearchForm() {
  const [searchTerm, setSearchTerm] = useState('');
  const { fetchDrinks } = useGlobalContext();

  const handleSubmit = (e) => {
    e.preventDefault();
  };

  const searchCocktail = () => {
    let timeoutId;
    return (e) => {
      const searchTerm = e.target.value;
      setSearchTerm(searchTerm);
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        fetchDrinks(searchTerm);
      }, 1000);
    };
  };
  const debounceSearchCocktail = useMemo(() => searchCocktail(), []);
  return (
    <section className='section search'>
      <form className='search-form' onSubmit={handleSubmit}>
        <div className='form-control'>
          <label htmlFor='name'>search your favorite cocktail</label>
          <input
            type='text'
            name='name'
            id='name'
            onChange={debounceSearchCocktail}
            value={searchTerm}
          />
        </div>
      </form>
    </section>
  );
}
```

#### Debounce with useEffect

```js
import React, { useEffect, useMemo, useState } from 'react';
import { useGlobalContext } from '../context';
export default function SearchForm() {
  const [searchTerm, setSearchTerm] = useState('');
  const [timeoutId, setTimeoutId] = useState(null);

  const { fetchDrinks } = useGlobalContext();

  const handleSubmit = (e) => {
    e.preventDefault();
  };

  const searchCocktail = (e) => {
    const searchTerm = e.target.value;
    setSearchTerm(searchTerm);
    clearTimeout(timeoutId);
    setTimeoutId(
      setTimeout(() => {
        // Call the API after the debounce timeout
        fetchDrinks(searchTerm);
      }, 1000)
    );
  };

  useEffect(() => {
    // Cleanup function to clear the timeout on unmount and re-render
    return () => {
      clearTimeout(timeoutId);
    };
  }, [timeoutId]);

  return (
    <section className='section search'>
      <form className='search-form' onSubmit={handleSubmit}>
        <div className='form-control'>
          <label htmlFor='name'>search your favorite cocktail</label>
          <input
            type='text'
            name='name'
            id='name'
            onChange={searchCocktail}
            value={searchTerm}
          />
        </div>
      </form>
    </section>
  );
}
```
