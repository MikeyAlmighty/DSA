# Arrays

```
array = ["apples", "bananas", "cucumbers", "dates", "elderberries"]
```

## Operations
* **Read**: Reading refers to looking something up at a particular spot within the data structure. 
With an `array`, this means looking up a value at a particular **index**.

* **Search**: Searching refers to looking for a particular value within a data structure.
With an `array`, this means looking to see if a particular value exists within the `array`, and if so, at which **index**.

* **Insert**: Insertion refers to adding a new value to our data structure. 
With an `array`, this means adding a new value to an additional slot within the `array`.

* **Delete**: Deletion refers to removing a value from our data structure.
With an `array`, this means removing one of the values from the `array`.

## Example

```go
package array

import (
	"errors"
)

type Array struct {
	Entries []string
}

func NewArray() *Array {
	return &Array{
		Entries: []string{},
	}
}
func (a *Array) GetValue(pos int) (string, error) {
	if pos < 0 || pos >= len(a.Entries) {
		return "", errors.New("invalid index")
	}
	return a.Entries[pos], nil
}

func (a *Array) Insert(value string) {
	a.Entries = append(a.Entries, value)
}

func (a *Array) Search(value string) (int, error) {
	for i, v := range a.Entries {
		if v == value {
			return i, nil
		}
	}
	return -1, errors.New("value not found")
}

func (a *Array) Remove(value string) (bool, error) {
	pos, err := a.Search(value)
	if err != nil {
		return false, err
	}
	a.Entries = append(a.Entries[:pos], a.Entries[pos+1:]...)
	// Alternative (using slices.Delete added in go 1.21+):
	// a.Entries = slices.Delete(a.Entries, pos, pos+1)
	return true, nil
}

```

```go
package array

import (
	"testing"

	"gotest.tools/assert"
)

func TestNewArray(t *testing.T) {
	a := NewArray()
	assert.DeepEqual(t, a.Entries, []string{})
}

func TestInsert(t *testing.T) {
	movies := NewArray()
	movies.Insert("Deadpool")
	movies.Insert("Deadpool 2")
	movies.Insert("Deadpool And Wolverine")

	assert.Equal(t, len(movies.Entries), 3)
	assert.DeepEqual(t, movies.Entries, []string{"Deadpool", "Deadpool 2", "Deadpool And Wolverine"})
}

func TestSearch(t *testing.T) {
	movies := NewArray()
	movies.Insert("Deadpool")
	movies.Insert("Deadpool 2")
	movies.Insert("Shrek")
	movies.Insert("Deadpool And Wolverine")

	pos, err := movies.Search("Shrek")

	assert.Equal(t, pos, 2)
	assert.Equal(t, err, nil)

	_, err = movies.Search("Shrek 2")
	assert.Error(t, err, "value not found")
}

func TestRemove(t *testing.T) {
	movies := NewArray()
	movies.Insert("Deadpool")
	movies.Insert("Deadpool 2")
	movies.Insert("Shrek")
	movies.Insert("Deadpool And Wolverine")

	ok, err := movies.Remove("Shrek")

	assert.Equal(t, ok, true)
	assert.Equal(t, err, nil)

	assert.DeepEqual(t, movies.Entries, []string{"Deadpool", "Deadpool 2", "Deadpool And Wolverine"})

	ok, err = movies.Remove("Lion King")
	assert.Equal(t, ok, false)
	assert.Error(t, err, "value not found")
}

func TestGetValue(t *testing.T) {
	movies := NewArray()
	movies.Insert("Deadpool")
	movies.Insert("Deadpool 2")
	movies.Insert("Shrek")
	movies.Insert("Deadpool And Wolverine")

	val, err := movies.GetValue(2)

	assert.Equal(t, val, "Shrek")
	assert.Equal(t, err, nil)

	val, err = movies.GetValue(9)

	assert.Error(t, err, "invalid index")
	assert.Equal(t, val, "")

	val, err = movies.GetValue(-1)

	assert.Error(t, err, "invalid index")
	assert.Equal(t, val, "")
}
```
