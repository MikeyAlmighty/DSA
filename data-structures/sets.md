# Sets
A **set** is a data structure that does **not** allow **duplicate values** to be contained within it.

## Operations
* **Read**: Reading refers to looking something up at a particular spot within the data structure. 
With an `array`, this means looking up a value at a particular **index**.

* **Search**: Searching refers to looking for a particular value within a data structure.
With an `array`, this means looking to see if a particular value exists within the `array`, and if so, at which **index**.

* **Insert**: Insertion refers to adding a new value to our data structure. 
With a `set`, every insertion into a set first requires a **search**.

* **Delete**: Deletion refers to removing a value from our data structure.
With an `array`, this means removing one of the values from the `array`.


## Example

```go
package set

import "errors"

type Set struct {
	Entries []string
}

func NewSet() *Set {
	return &Set{
		Entries: []string{},
	}
}

func (s *Set) GetValue(pos int) (string, error) {
	if pos < 0 || pos >= len(s.Entries) {
		return "", errors.New("invalid index")
	}
	return s.Entries[pos], nil
}

func (s *Set) Insert(value string) error {
	// Unlike Arrays where we can just insert,
	// Sets require a search before insertion (Check for dupe)
	pos, _ := s.Search(value)

	if pos >= 0 {
		return errors.New("value exists")
	}

	s.Entries = append(s.Entries, value)

	return nil
}

func (s *Set) Search(value string) (int, error) {
	for i, v := range s.Entries {
		if v == value {
			return i, nil
		}
	}
	return -1, errors.New("value not found")
}

func (s *Set) Remove(value string) (bool, error) {
	pos, err := s.Search(value)
	if err != nil {
		return false, err
	}
	s.Entries = append(s.Entries[:pos], s.Entries[pos+1:]...)
	// Alternative (using slices.Delete added in go 1.21 +):
	// a.Entries = slices.Delete(a.Entries, pos, pos+1)
	return true, nil
}

func (s *Set) Len() int {
	return len(s.Entries)
}
```


```go
package set

import (
	"testing"

	"gotest.tools/assert"
)

func TestNewSet(t *testing.T) {
	s := NewSet()
	assert.DeepEqual(t, s.Entries, []string{})
}

func TestInsert(t *testing.T) {
	s := NewSet()
	err := s.Insert("Deadpool")

	assert.Equal(t, err, nil)
	assert.DeepEqual(t, s.Entries, []string{"Deadpool"})

	err = s.Insert("Deadpool")
	assert.Error(t, err, "value exists")
}

func TestLen(t *testing.T) {
	s := NewSet()
	s.Insert("Deadpool")

	assert.Equal(t, s.Len(), 1)

	s.Insert("Deadpool")
	assert.Equal(t, s.Len(), 1)

	s.Insert("Deadpool 2")
	assert.Equal(t, s.Len(), 2)
}
```
