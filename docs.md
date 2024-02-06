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

## Component

### Lookup

#### HTML

-   Field
    Code snippet :

```HTML
<!--#region lookup Field Name Code -->
<div class="col-md-4">
  <div class="form-group">
    <label class="control-label labelmodule">Field Name *</label>
    <div class="col-md-12 buttons">
      <span>
        <button
          type="button" class="btn btn-icon btn-outline-default btn-round btn-md col-md-4" (click)="btnlookupfieldname()" data-target="#lookupModalFieldName"
          data-toggle="modal" href="#">
          <i class="fa fa-search-plus"></i></button>
          <a>{{model.description}}</a>
        <input type="hidden" name="p_code" [ngModel]="model.code" required
          #code="ngModel" />
        <input type="hidden" name="p_description" [ngModel]="model.description" />
      </span>
    </div>
    <small [hidden]="code.valid || (code.pristine && !formID.submitted)"
      class="text-danger">
      Field Name is required
    </small>
  </div>
</div>
<!--#endregion lookup Field Name code -->
```

-   Modal Table

```HTML
<!--#region modal lookup Field Name-->
<div class="modal bd-example-modal-lg" tabindex="-1" aria-hidden="true" id="lookupModalFieldName"
  style="margin-top: -65px">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <button class="close" data-dismiss="modal">&times;</button>
      </div>
      <div class="modal-body" style="overflow:hidden;">
        <div class="col-md-12">
          <div class="card-body col-md-12">
            <table id="datatablelookupfieldname" class="table table-striped table-bordered hover" cellspacing="0"
              style="min-width: 100%;">
              <thead>
                <tr>
                  <th style="display: none;"></th>
                  <th class="disabled-sorting">No</th>
                  <th>Code</th>
                  <th>Description</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                <tr *ngFor="let row of lookupfieldname;let i=index ">
                  <td style="display: none;"></td>
                  <td style="text-align: center;">{{lookupfieldname.numberIndex+i+1}}</td>
                  <td style="width: 20%; text-align: center;">{{ row.code}}</td>
                  <td style="width: 80%;">{{ row.description }}</td>
                  <td class="text-center">
                    <button class="btn btn-outline-default btn-round"
                      (click)="btnSelectRowFieldName(row.code,row.description)">Select</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
<!--#endregion modal lookup Field Name-->
```

#### TS

```Typescript
//#region lookup field name
btnlookupfieldname() {
  $('#datatablelookupfieldname').DataTable().clear().destroy();
  $('#datatablelookupfieldname').DataTable({
    pagingType: 'first_last_numbers',
    pageLength: 5,
    processing: true,
    serverSide: true,
    responsive: true,
    lengthChange: false, // hide lengthmenu
    searching: true, // if false (hide search box)
    ajax: (dtParameters: any, callback) => {
      // param tambahan untuk getrows dynamic
      dtParameters.paramTamp = [];
      dtParameters.paramTamp.push({
        'default': ''
      });
      // end param tambahan untuk getrows dynamic

      this.dalservice.Getrows(dtParameters, this.APIController, this.APIRouteForLookup)
      .subscribe(
        resp => {
          const parse = JSON.parse(resp);
          this.lookupfieldname = parse.data;

          if (parse.data != null) {
            this.lookupfieldname.numberIndex = dtParameters.start;
          }

          callback({
            draw: parse.draw,
            recordsTotal: parse.recordsTotal,
            recordsFiltered: parse.recordsFiltered,
            data: []
          });
        }, err => console.log('There was an error while retrieving Data(API) !!!' + err));
    },
    columnDefs: [{ orderable: false, width: '5%', targets: [0, 1] }], // for disabled coloumn
    language: {
      search: '_INPUT_',
      searchPlaceholder: 'Search records',
      infoEmpty: '<p style="color:red;" > No Data Available !</p> '
    },
    searchDelay: 800 // prevent bug search
  });
}

btnSelectRowFieldName(code: String, description: String) {
  this.model.code = code;
  this.model.description = description;

  $('#lookupModalFieldName').modal('hide');
}
//#endregion lookup field name
```

### IIFE (Immediately Invoked Function Expression)

```Typescript
let J = 0, vm = this;

(function loopactivity() {
  if (J < vm.listData.length) {
    const codeData = vm.listData[J];

    vm.dataTamp = [{
        'p_code': codeData
    }];

    vm.dalservice.Process(vm.dataTamp, vm.APIController, vm.APIRouteForProcess)
        .subscribe(
            res => {
                const parse = JSON.parse(res);

                if (parse.result === 1) {
                    if (J + 1 === vm.listData.length) {
                        vm.showSpinner = false;
                        vm.showNotification('bottom', 'right', 'success');

                        $('#datatableID').DataTable().ajax.reload();
                    } else {
                        J++;
                        loopactivity();
                    }
                } else {
                    vm.showSpinner = false;
                    vm.swalPopUpMsg(parse.data);

                    $('#datatableID').DataTable().ajax.reload();
                }
            },
            error => {
                vm.showSpinner = false;

                const parse = JSON.parse(error);
                vm.swalPopUpMsg(parse.data);
            });
  }
})();
```

### Button

#### HTML

```HTML
<button type="button" class="btn btn-success btn-round" data-role="[role-code]" (click)="btnActivity(param)">Activity</button>
```

#### TS
```Typescript
//#region button Activity
btnActivity(code: string) {
  // param tambahan
  this.dataRoleTamp = [{
    'p_code': code,
    'action': ''
  }];
  // param tambahan

  swal({
    title: 'Are you sure?',
    type: 'warning',
    showCancelButton: true,
    confirmButtonClass: 'btn btn-success',
    cancelButtonClass: 'btn btn-danger',
    confirmButtonText: this._deleteconf,

    buttonsStyling: false
  }).then((result) => {
    this.showSpinner = true;

    if (result.value) {
      this.dalservice.ExecSp(this.dataRoleTamp, this.APIController, this.APIRouteForActivity)
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
    } else {
      this.showSpinner = false;
    }
  });
}
//#endregion button Activity
```

### Input tag

-   Required tag

```HTML
<!-- #region field name -->
<div class="col-md-4">
  <div class="form-group">
    <label for="column" class="control-label">Field Name *</label>
    <input type="text" class="form-control" [ngModel]="model.column" #column="ngModel" name="p_column" maxlength="250" required [readonly]="isReadOnly">
    <small
      *ngIf="(column.errors?.required && column.touched) || (column.errors?.required && branchparamForm.submitted)"
      class="text-danger">
      Field Name is required
    </small>
  </div>
</div>
<!-- #endregion field name -->
```
