# `mulle_concurrent_pointerarray`

`mulle_concurrent_pointerarray` is a mutable array of pointers that can only
grow. Such an array can be shared with multiple threads, that can access the
array without locking. Its limitations are its strength, as it makes the
API very simple and safe.


The following operations should be executed in single-threaded fashion:

* `mulle_concurrent_pointerarray_init`
* `mulle_concurrent_pointerarray_done`

The following operations are fine in multi-threaded environments:

* `mulle_concurrent_pointerarray_add`
* `mulle_concurrent_pointerarray_get`
* `mulle_concurrent_pointerarray_enumerate`
* `mulle_concurrent_pointerarray_reverseenumerate`
* `mulle_concurrent_pointerarray_map`
* `mulle_concurrent_pointerarray_find`
* `mulle_concurrent_pointerarray_get_count`
* `mulle_concurrent_pointerarray_get_size`







### `mulle_concurrent_pointerarray_init`

```
int   mulle_concurrent_pointerarray_init( struct mulle_concurrent_pointerarray *array,
                                          unsigned int size,
                                          struct mulle_allocator *allocator)
```

Initialize `array`, with a starting `size` of elements. `allocator` will be
used to allocate and free memory during the lifetime of `array`.  You can pass in
for `allocator` to use the default. This needs to be called in **single-threaded** fashion.

##### Return Values:

*   0      : OK
*   EINVAL : invalid argument
*   ENOMEM : out of memory


### `mulle_concurrent_pointerarray_done`

```
void  mulle_concurrent_pointerarray_done( struct mulle_concurrent_pointerarray *array)
```

This will free all allocated resources `array`. It will not **free** `array`
itself though. `array` must be a valid pointer. This needs to be called in **single-threaded** fashion.




### `mulle_concurrent_pointerarray_add`

```
int  mulle_concurrent_pointerarray_add( struct mulle_concurrent_pointerarray *array,
                                        void *value)
```

Add value to the end of the array.
`value` can be any `void *` except `NULL` or `(void *) INTPTR_MIN`. It will
not get dereferenced by the pointerarray.


##### Return Values:

*   0      : OK
*   EINVAL : invalid argument
*   ENOMEM : out of memory


### `mulle_concurrent_pointerarray_get`

```
void   *mulle_concurrent_pointerarray_get( struct mulle_concurrent_pointerarray *array,
                                           unsigned int index)
```

Get value at `index` of array.

##### Return Values:

*   NULL  : not found (invalid argument)
*   otherwise the value


### `mulle_concurrent_pointerarray_get_size`

```
unsigned int  mulle_concurrent_pointerarray_get_size( struct mulle_concurrent_pointerarray *array)
```

This gives you the capacity of `array`. This value is close to
meaningless, when the array is accessed in multi-threaded fashion.


### `mulle_concurrent_pointerarray_get_count`

```
unsigned int   mulle_concurrent_pointerarray_get_count( struct mulle_concurrent_pointerarray *array);
```

This gives you the current number of entries in `array`. As the array can only
grow this value is useful, but possibly outdated.


## `mulle_concurrent_pointerarrayenumerator`


The following operations should be executed by a single thread only, but the environment can be multi-threaded:

* `mulle_concurrent_pointerarrayenumerator_next`
* `mulle_concurrent_pointerarrayenumerator_done`


### `mulle_concurrent_pointerarray_enumerate`

```
struct mulle_concurrent_pointerarrayenumerator  mulle_concurrent_pointerarray_enumerate( struct mulle_concurrent_pointerarray *array)
```

Enumerate a pointerarray (0 to n-1). This works reliably even in multi-threaded
environments. The enumerator itself should not be shared with other
threads though.

Here is a simple usage example:


```
   struct mulle_concurrent_pointerarray             *array;
   struct mulle_concurrent_pointerarrayenumerator   rover;
   void                                             *value;

   rover = mulle_concurrent_pointerarray_enumerate( array);
   while( value = mulle_concurrent_pointerarrayenumerator_next( &rover))
   {
      printf( "%p\n", value);
   }
   mulle_concurrent_pointerarrayenumerator_done( &rover);
```

### `mulle_concurrent_pointerarrayenumerator_next`

```
void   *mulle_concurrent_pointerarrayenumerator_next( struct mulle_concurrent_pointerarrayenumerator *rover)
```

Get the next value from the enumerator.

##### Return Values:

*   NULL  : nothing left
*   otherwise the value


### `mulle_concurrent_pointerarrayenumerator_done`

```
void   mulle_concurrent_pointerarrayenumerator_done( struct mulle_concurrent_pointerarrayenumerator *rover)
```

Mark the end of the enumerator lifetime. It's a mere conventional function.
It may be left out.


## `mulle_concurrent_pointerarrayreverseenumerator`

The following operations should be executed by a single thread only, but the environment can be multi-threaded:

* `mulle_concurrent_pointerarrayreverseenumerator_next`
* `mulle_concurrent_pointerarrayreverseenumerator_done`


### `mulle_concurrent_pointerarray_reverseenumerate`

```
struct mulle_concurrent_pointerarrayreverseenumerator  mulle_concurrent_pointerarray_reverseenumerate( struct mulle_concurrent_pointerarray *array, unsigned int n)
```

Reverse enumerate a pointerarray (n-1 to 0). You have to supply the `n`.
This works reliably even in multi-threaded environments, but the enumerator
itself should not be shared with other threads though.


### `mulle_concurrent_pointerarrayreverseenumerator_next`

```
void   *mulle_concurrent_pointerarrayreverseenumerator_next( struct mulle_concurrent_pointerarrayreverseenumerator *rover)
```

Get the next value from the enumerator.

##### Return Values:

*   NULL  : nothing left
*   otherwise the value


### `mulle_concurrent_pointerarrayreverseenumerator_done`

```
void   mulle_concurrent_pointerarrayreverseenumerator_done( struct mulle_concurrent_pointerarrayreverseenumerator *rover)
```

Mark the end of the enumerator lifetime. It's a mere conventional function.
It may be left out.

