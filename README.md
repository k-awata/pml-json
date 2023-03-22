# pml-json

pml-json provides functionality to encode PML objects to JSON strings and decode JSON strings to PML objects.

## Installation

To install this macro, follow these steps:

1. Place `json.pmlobj` in a directory specified by the `PMLLIB` environment variable.

2. Open an AVEVA product.

3. Enter the following command in the Command Window:

   ```pml
   pml rehash all
   ```

## Usage

### `.String(!object is ANY) is STRING`

String method returns the JSON string of the specified object.

- Example:

  ```pml
  !json = object JSON()
  !obj = object POSITION('E 100mm N 200mm U 300mm WRT /*')
  !result = !json.String(!obj)
  $P$!result
  ```

- Result in the console:

  ```json
  {"east":100,"north":200,"origin":"/*","up":300}
  ```

### `.Lines(!object is ANY) is ARRAY`

Lines method returns an array of the indented JSON strings from the specified object.

- Example:

  ```pml
  !file = object FILE('%temp%\sample.json')
  !json = object JSON()
  !obj = object VOLUME('E 100mm N 200mm U 300mm WRT /* TO E 400mm N 500mm U 600mm WRT /*')
  !file.WriteFile('OVERWRITE', !json.Lines(!obj))
  ```

- Result in `%temp%\sample.json`:

  ```json
  {
    "from": {
      "east": 100,
      "north": 200,
      "origin": "/*",
      "up": 300
    },
    "to": {
      "east": 400,
      "north": 500,
      "origin": "/*",
      "up": 600
    }
  }
  ```

### `.Lines(!object is ANY, !indent is STRING) is ARRAY`

Lines method returns an array of the indented JSON strings from the specified object with the specified indent string.

- Example:

  ```pml
  !file = object FILE('%temp%\sample.json')
  !json = object JSON()
  !obj = object VOLUME('E 100mm N 200mm U 300mm WRT /* TO E 400mm N 500mm U 600mm WRT /*')
  !file.WriteFile('OVERWRITE', !json.Lines(!obj, '    '))
  ```

- Result in `%temp%\sample.json`:

  ```json
  {
      "from": {
          "east": 100,
          "north": 200,
          "origin": "/*",
          "up": 300
      },
      "to": {
          "east": 400,
          "north": 500,
          "origin": "/*",
          "up": 600
      }
  }
  ```

### `.Parse(!json is STRING, !object is ANY)`

Parse method parses a JSON string and stores the result in the specified object.

- Example:

  ```pml
  !json = object JSON()
  !obj = object POSITION()
  !json.Parse('{"east":100,"north":200,"up":300,"origin":"/*"}', !obj)
  q var !obj
  ```

- Result in the console:

  ```text
  <POSITION> E 100mm N 200mm U 300mm WRT /*
     EAST <REAL> 100
     NORTH <REAL> 200
     ORIGIN <DBREF> =****/**** (Refno depends on DB)
     UP <REAL> 300
  ```

### `.Parse(!json is ARRAY, !object is ANY)`

Parse method parses an array of JSON strings and stores the result in the specified object.

- Example:

  ```pml
  !json = object JSON()
  !file = object FILE('%temp%\sample.json')
  !obj = object DICTIONARY()
  !json.Parse(!file.ReadFile(), !obj)
  q var !obj.keys !obj.values
  q var !obj.values[1].keys !obj.values[1].values
  q var !obj.values[2].keys !obj.values[2].values
  ```

- Contents of `%temp%\sample.json`:

  ```json
  {
    "from": {
      "east": 100,
      "north": 200,
      "up": 300,
      "origin": "/*"
    },
    "to": {
      "east": 400,
      "north": 500,
      "up": 600,
      "origin": "/*"
    }
  }
  ```

- Result in the console:

  ```text
  <ARRAY>
     [1]  <STRING> 'from'
     [2]  <STRING> 'to'
  <ARRAY>
     [1]  <DICTIONARY> DICTIONARY
     [2]  <DICTIONARY> DICTIONARY
  
  <ARRAY>
     [1]  <STRING> 'east'
     [2]  <STRING> 'north'
     [3]  <STRING> 'up'
     [4]  <STRING> 'origin'
  <ARRAY>
     [1]  <REAL> 100
     [2]  <REAL> 200
     [3]  <REAL> 300
     [4]  <STRING> '/*'
  
  <ARRAY>
     [1]  <STRING> 'east'
     [2]  <STRING> 'north'
     [3]  <STRING> 'up'
     [4]  <STRING> 'origin'
  <ARRAY>
     [1]  <REAL> 400
     [2]  <REAL> 500
     [3]  <REAL> 600
     [4]  <STRING> '/*'
  ```

## Tests

The test cases use [PML Unit](https://github.com/PoByBolek/PmlUnit) on Everything3D 2.1.

## License

[MIT License](LICENSE)
