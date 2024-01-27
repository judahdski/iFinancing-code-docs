# Docs
i-Financing standard code documentation.

## Angular

#### Getrows function

#### Getrow function
Code snippet :

```TypeScript
  // #param tambahan untuk getrow dynamic
  this.dataTamp = [{
    'p_param': param
  }];
  // end param tambahan untuk getrow dynamic

  this.dalservice.Getrow(this.dataTamp, this.APIController, this.APIRouteForGetRow)
    .subscribe(
      res => {
        const parse = JSON.parse(res);
        const parsedata = parse.data[0];

        // do something

        // mapper dbtoui
        Object.assign(this.model, parsedata);
        // end mapper dbtoui

        this.showSpinner = false;
      },
      error => {
        console.log('There was an error while Retrieving Data(API) !!!' + error);
      });
```

#### Insert & Update function

Code snippet :

```TypeScript
  // For insert and update
  this.formID = formID; // use ID of <form>
  const usersJson: any[] = Array.of(this.formID);

  this.dalservice.Insert(usersJson, this.APIController, this.APIRouteForInsert)
    .subscribe(
      res => {
        const parse = JSON.parse(res);

        if (parse.result === 1) {
          this.showSpinner = false;
          this.showNotification('bottom', 'right', 'success');

          // do something
        } else {
          this.showSpinner = false;
          this.swalPopUpMsg(parse.data);
        }
      },
      error => {
        this.showSpinner = false;

        const parse = JSON.parse(error);
        this.swalPopUpMsg(parse.data);
      });

  this.dalservice.Update(usersJson, this.APIController, this.APIRouteForUpdate)
    .subscribe(
      res => {
        const parse = JSON.parse(res);

        if (parse.result === 1) {
          this.showSpinner = false;
          this.showNotification('bottom', 'right', 'success');
          
          // do something
        } else {
          this.showSpinner = false;
          this.swalPopUpMsg(parse.data);
        }
      },
      error => {
        this.showSpinner = false;
        const parse = JSON.parse(error);
        this.swalPopUpMsg(parse.data);
      });
```

#### Delete function
Code snippet :

```TypeScript
  // param tambahan untuk getrow dynamic
  this.dataTamp = [{
      'p_param': param
  }];
  // end param tambahan untuk getrow dynamic

  this.dalservice.Delete(this.dataTamp, this.APIController, this.APIRouteForGetDelete)
      .subscribe(
          res => {
              const parse = JSON.parse(res);

              if (parse.result === 1) {
                // do something
              } else {
                  this.showSpinner = false;
                  this.swalPopUpMsg(parse.data);
              }
          },
          error => {
              this.showSpinner = false;
              const parse = JSON.parse(error);
              this.swalPopUpMsg(parse.data);
          });
```
<br>

## API

### Getrows
``` c#
[HttpPost]
[Route("GetRows")]
public async Task<IActionResult> GetRows([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        var Jsondata = JsonConvert.SerializeObject(data);
        JObject JsonObject = JObject.Parse(Jsondata);

        return Ok(base.GetRows(JsonObject, _tableName, _spNameForGetRows));
    });
}
```

### Getrow
```c#
[HttpPost]
[Route("GetRow")]
public async Task<IActionResult> GetRow([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.GetRow((JArray)data, _tableName));
    });
}
```

### Insert
##### Insert with output code
```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.InsertOutputCode((JArray)data, _tableName));
    });
}
```

##### Insert with output ID
```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.InsertOutputId((JArray)data, _tableName));
    });
}
```

##### Insert without any output
```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Insert((JArray)data, _tableName));
    });
}
```

### Update
```c#
[HttpPost]
[Route("Update")]
public async Task<IActionResult> Update([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Update((JArray)data, _tableName));
    });
}
```

### Delete
```c#
[HttpPost]
[Route("Delete")]
public async Task<IActionResult> Delete([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Delete((JArray)data, _tableName));
    });
}
```

### Exec SP
```c#
[HttpPost]
[Route("ExecSp")]
public async Task<IActionResult> ExecSp([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.ExecSp((JArray)data, _spName));
    });
}
```
