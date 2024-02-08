# Docs

i-Financing standard field name documentation.

## 5 SP Dasar

### Angular

###### Getrows function

_Code snippet :_

```Typescript
this.dalservice.Getrows(dtParameters, this.APIController, this.APIRouteForGetRows)
  .subscribe(
    resp => {
      const parse = JSON.parse(resp)
      this.listscreenname = parse.data;

      if (parse.data != null) {
        this.listscreenname.numberIndex = dtParameters.start;
      }
      this.showSpinner = false;

      // if use checkAll use this
      $('#checkalltable').prop('checked', false);
      // end checkall

      callback({
        draw: parse.draw,
        recordsTotal: parse.recordsTotal,
        recordsFiltered: parse.recordsFiltered,
        data: []
      });
    }, err => console.log('There was an error while retrieving Data(API)' + err));
```

###### Getrow function

_Code snippet :_

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

###### Insert & Update function

_Code snippet :_

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

###### Delete function

_Code snippet :_

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

### API

###### Getrows

```c#
[HttpPost]
[Route("GetRows")]
public async Task<IActionResult> GetRows([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        var Jsondata = JsonConvert.SerializeObject(data);
        JObject JsonObject = JObject.Parse(Jsondata);

        return Ok(base.GetRows(JsonObject, _tableField Name, _spField NameForGetRows));
    });
}
```

###### Getrow

```c#
[HttpPost]
[Route("GetRow")]
public async Task<IActionResult> GetRow([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.GetRow((JArray)data, _tableField Name));
    });
}
```

###### Insert

-   Insert with output field name

```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.InsertOutputCode((JArray)data, _tableField Name));
    });
}
```

-   Insert with output ID

```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.InsertOutputId((JArray)data, _tableField Name));
    });
}
```

-   Insert without any output

```c#
[HttpPost]
[Route("Insert")]
public async Task<IActionResult> Insert([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Insert((JArray)data, _tableField Name));
    });
}
```

###### Update

```c#
[HttpPost]
[Route("Update")]
public async Task<IActionResult> Update([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Update((JArray)data, _tableField Name));
    });
}
```

###### Delete

```c#
[HttpPost]
[Route("Delete")]
public async Task<IActionResult> Delete([FromBody] dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.Delete((JArray)data, _tableField Name));
    });
}
```

###### Exec SP

```c#
[HttpPost]
[Route("ExecSp")]
public async Task<IActionResult> ExecSp([FromBody]dynamic data)
{
    return await Task.Run(() =>
    {
        return Ok(base.ExecSp((JArray)data, _spField Name));
    });
}
```

### Database (SP SQL)

## Screen

### List Screen

```Typescript
@Component({
  moduleId: module.id,
  selector: 'app-root',
  templateUrl: './screennamelist.component.html'
})

export class ScreenNamelistComponent extends BaseComponent implements OnInit {
  // variable
  public isBreak: Boolean = false;
  public isReadOnly: Boolean = false;
  public listscreenname: any = [];
  private dataTamp: any = [];

  // API variables
  private APIController: String = 'ScreenName';

  private APIRouteForGetRows: String = 'GetRows';
  private APIRouteForDelete: String = 'Delete';

  private RoleAccessCode = 'R00018520000000A'; // role access

  // checklist
  public selectedAllTable: any;
  public checkedList: any = [];

  // spinner
  showSpinner: Boolean = true;
  // end

  // ini buat datatables
  @ViewChild(DataTableDirective)
  dtElement: DataTableDirective;
  dtOptions: DataTables.Settings = {};

  constructor(
    private dalservice: DALService,
    public route: Router,
    private _location: Location,
    private _elementRef: ElementRef
  ) { super(); }

  ngOnInit() {
    this.callGetRole(this.userId, this._elementRef, this.dalservice, this.RoleAccessCode, this.route);
    this.compoSide(this._location, this._elementRef, this.route);

    this.loadData();

    setTimeout(() => {
      this.TransactionLock(this.userId, '', '', this.dalservice);
    }, 100);
  }

  //#region load all data
  loadData() {
    this.dtOptions = {
      pagingType: 'full_numbers',
      responsive: true,
      serverSide: true,
      processing: true,
      paging: true,
      'lengthMenu': [
        [10, 25, 50, 100],
        [10, 25, 50, 100]
      ],
      ajax: (dtParameters: any, callback) => {
        this.dalservice.Getrows(dtParameters, this.APIController, this.APIRouteForGetRows)
        .subscribe(
          resp => {
            const parse = JSON.parse(resp)
            this.listscreenname = parse.data;

            if (parse.data != null) {
              this.listscreenname.numberIndex = dtParameters.start;
            }
            this.showSpinner = false;

            // if use checkAll use this
            $('#checkalltable').prop('checked', false);
            // end checkall

            callback({
              draw: parse.draw,
              recordsTotal: parse.recordsTotal,
              recordsFiltered: parse.recordsFiltered,
              data: []
            });
          }, err => console.log('There was an error while retrieving Data(API)' + err));
      },
      columnDefs: [{ orderable: false, width: '5%', targets: [0, 1] }],
      order:[[2, 'desc']],
      language: {
        search: '_INPUT_',
        searchPlaceholder: 'Search records',
        infoEmpty: '<p style="color:red;" > No Data Available !</p> '
      },
      searchDelay: 800
    }
  }
  //#endregion load all data

  //#region button add
  btnAdd() {
    this.route.navigate(['/path/to/add/screen']);
  }
  //#endregion button add

  //#region button edit
  btnEdit(codeEdit: string) {
    this.route.navigate(['/path/to/edit/screen', codeEdit]);
  }
  //#endregion button edit

  //#region checkall table delete
  btnDeleteAll() {
    this.isBreak = false;
    this.checkedList = [];

    for (let i = 0; i < this.listscreenname.length; i++) {
      if (this.listscreenname[i].selected) {
        this.checkedList.push(this.listscreenname[i].code);
      }
    }

    // jika tidak di checklist
    if (this.checkedList.length === 0) {
      swal({
        title: this._listdialogconf,
        buttonsStyling: false,
        confirmButtonClass: 'btn btn-danger'
      }).catch(swal.noop)
      return
    }

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
        this.dataTamp = [];

        for (let J = 0; J < this.checkedList.length; J++) {
          // param tambahan untuk getrow dynamic
          this.dataTamp = [{
            'p_code': this.checkedList[J]
          }];
          // end param tambahan untuk getrow dynamic

          this.dalservice.Delete(this.dataTamp, this.APIController, this.APIRouteForDelete)
            .subscribe(
              res => {
                const parse = JSON.parse(res);

                if (parse.result === 1) {
                  if (J + 1 === this.checkedList.length) {
                    this.showSpinner = false;
                    $('#datatableScreenNameList').DataTable().ajax.reload();
                    this.showNotification('bottom', 'right', 'success');
                  }
                } else {
                  this.isBreak = true;
                  this.showSpinner = false;
                  $('#datatableScreenNameList').DataTable().ajax.reload();
                  this.swalPopUpMsg(parse.data)
                }
              },
              error => {
                this.isBreak = true;
                this.showSpinner = false;

                const parse = JSON.parse(error);
                this.swalPopUpMsg(parse.data)
              });
          if (this.isBreak) {
            break;
          }
        }
      } else {
        this.showSpinner = false;
      }
    });
  }

  selectAllTable() {
    for (let i = 0; i < this.listscreenname.length; i++) {
      this.listscreenname[i].selected = this.selectedAllTable;
    }
  }

  checkIfAllTableSelected() {
    this.selectedAllTable = this.listscreenname.every(function (item: any) {
      return item.selected === true;
    })
  }
  //#endregion checkall table delete
}
```

```HTML
<router-outlet></router-outlet>

<div id="hideMainPanel" class="main-content">
  <!-- spinner region -->
  <div class="panel-container-spinner" *ngIf="showSpinner">
    <app-spinner class="spinner-container"></app-spinner>
  </div>
  <!-- end spinner region -->

  <div class="row">
    <div class="col-md-12">
      <div class="card">
        <!--#region watermark-->
        <div class="watermark" [hidden]="this.isWatermark === '0'">
          <h6>{{this.companyName}}</h6>
          <h6>{{this.userName}}</h6>
          <h6>{{this.sysDate | date}} {{this.currentTime}}</h6>
        </div>
        <!--#endregion watermark-->

        <div class="card-header">
          <h5 class="card-title">Screen Name List</h5>
        </div>
        <div class="card-body">
          <div class="toolbar">
            <!--#region button add, delete-->
            <button class="btn btn-info btn-round" (click)="btnAdd()" id="button" data-role="R00018520001853C">
              Add
            </button>
            <button hidden class="btn btn-danger btn-round" (click)="btnDeleteAll()" data-role="R00018520001853D">
              Delete
            </button>
            <!--#endregion button add, delete-->
          </div>
          <!--#region list view-->

          <table id="datatableScreenNameList" datatable [dtOptions]="dtOptions"
            class="table table-striped table-bordered row-border hover" cellspacing="0" width="100%">
            <thead>
              <tr>
                <th class="disabled-sorting">No</th>
                <th class="disabled-sorting">
                  <div class="form-check">
                    <label class="form-check-label">
                      <input class="form-check-input" type="checkbox" id="checkalltable" [(ngModel)]="selectedAllTable"
                        (change)="selectAllTable();" />
                      <span class="form-check-sign"></span>
                    </label>
                  </div>
                </th>
                <th>Code</th>
                <th>Name</th>
                <th>Active</th>
                <th hidden></th>
                <th class="text-align: center;">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr *ngFor="let row of listscreenname;let i=index ">
                <td style="text-align: center;">{{listscreenname.numberIndex+i+1}}</td>
                <td class="disabled-sorting if-no-edit-btn">
                  <div class="form-check">
                    <label class="form-check-label">
                      <input class="form-check-input" type="checkbox" [(ngModel)]="row.selected"
                        (change)="checkIfAllTableSelected($event, row.code);" value="{{row.code}}" name="p_code">
                      <span class="form-check-sign"></span>
                    </label>
                  </div>
                </td>
                <td style="text-align: center; width: 10%;">{{row.code}}</td>
                <td style="text-align: left; width: 30%;">{{row.name}}</td>
                <td style="text-align: center; width: 10%;">{{row.is_active}}</td>
                <td hidden></td>
                <td style="text-align: center;">
                  <a class="btn btn-warning btn-link btn-icon btn-sm edit" title="Edit" (click)="btnEdit(row.code)">
                    <i class="fa fa-edit"></i>
                  </a>
                </td>
              </tr>
            </tbody>
          </table>
          <!--#endregion list view-->
        </div>
      </div>
    </div>
  </div>
</div>
```

### Detail Screen

```Typescript
@Component({
  moduleId: module.id,
  selector: 'app-root',
  templateUrl: './screennamedetail.component.html'
})

export class ScreenNamedetailComponent extends BaseComponent implements OnInit {
  // get param from url
  param = this.getRouteparam.snapshot.paramMap.get('id');

  // variable
  public isReadOnly: Boolean = false;
  public lookupmodulecode: any = [];
  private screennamedetailData: any = [];
  private dataTamp: any = [];

  // API variables
  private APIController: String = 'ScreenName';

  private APIRouteForGetRow: String = 'Getrow';
  private APIRouteForInsert: String = 'Insert';
  private APIRouteForUpdate: String = 'Update';

  private RoleAccessCode = 'R00018520000000A'; // role access

  // form 2 way binding
  model: any = {};
  // checklist

  // spinner
  showSpinner: Boolean = true;
  // end

  // datatable
  dtOptions: DataTables.Settings = {};

  constructor(
    private dalservice: DALService,
    public getRouteparam: ActivatedRoute,
    public route: Router,
    private _elementRef: ElementRef
  ) { super(); }

  ngOnInit() {
    this.callGetRole(this.userId, this._elementRef, this.dalservice, this.RoleAccessCode, this.route);

    if (this.param != null) {
      this.isReadOnly = true;

      // call web service
      this.callGetrow();
    } else {
      this.showSpinner = false;
    }

    setTimeout(() => {
      this.TransactionLock(this.userId, this.param, '', this.dalservice);
    }, 100);
  }

  //#region getrow data
  callGetrow() {
    // param tambahan untuk getrow dynamic
    this.dataTamp = [{
      'p_code': this.param,
    }];
    // end param tambahan untuk getrow dynamic

    this.dalservice.Getrow(this.dataTamp, this.APIController, this.APIRouteForGetRow)
      .subscribe(
        res => {
          const parse = JSON.parse(res);
          const parsedata = this.getrowNgb(parse.data[0]);

          // checkbox
          if (parsedata.is_active === '1') {
            parsedata.is_active = true;
          } else {
            parsedata.is_active = false;
          }
          // end checkbox

          // mapper dbtoui
          Object.assign(this.model, parsedata);
          // end mapper dbtoui

          this.showSpinner = false;
        },
        error => {
          console.log('There was an error while Retrieving Data(API)' + error);
        });
  }
  //#endregion getrow data

  //#region  form submit
  onFormSubmit(screennamedetailForm: NgForm, isValid: boolean) {
    // validation form submit
    if (!isValid) {
      swal({
        title: 'Warning',
        text: 'Please Fill a Mandatory Field OR Format Is Invalid',
        buttonsStyling: false,
        confirmButtonClass: 'btn btn-warning',
        type: 'warning'
      }).catch(swal.noop)
      return;
    } else {
      this.showSpinner = true;
    }

    this.screennamedetailData = screennamedetailForm;

    if (this.screennamedetailData.p_is_active == null) {
      this.screennamedetailData.p_is_active = false;
    }
    if (this.screennamedetailData.p_is_editable == null) {
      this.screennamedetailData.p_is_editable = false;
    }

    const usersJson: any[] = Array.of(this.screennamedetailData);

    if (this.param != null) {
      // call web service
      this.dalservice.Update(usersJson, this.APIController, this.APIRouteForUpdate)
        .subscribe(
          res => {
            this.showSpinner = false;
            const parse = JSON.parse(res);

            if (parse.result === 1) {
              this.showNotification('bottom', 'right', 'success');
              this.callGetrow()
            } else {
              this.swalPopUpMsg(parse.data);
            }
          },
          error => {
            this.showSpinner = false;
            const parse = JSON.parse(error);
            this.swalPopUpMsg(parse.data);
          });
    } else {
      this.dalservice.Insert(usersJson, this.APIController, this.APIRouteForInsert)
        .subscribe(
          res => {
            this.showSpinner = false;
            const parse = JSON.parse(res);

            if (parse.result === 1) {
              this.showNotification('bottom', 'right', 'success');
              this.route.navigate(['/path/to/edit/screen', parse.code]);
            } else {
              this.swalPopUpMsg(parse.data);
            }
          },
          error => {
            this.showSpinner = false;
            const parse = JSON.parse(error);
            this.swalPopUpMsg(parse.data);
          });
    }
  }
  //#endregion form submit

  //#region button back
  btnBack() {
    this.route.navigate(['/path/to/list/screen']);
    $('#datatableScreenNameList').DataTable().ajax.reload();
  }
  //#endregion button back
}
```

```HTML
<div class="main-content">
  <!-- spinner region -->
  <div class="panel-container-spinner" *ngIf="showSpinner">
    <app-spinner class="spinner-container"></app-spinner>
  </div>
  <!-- end spinner region -->

  <div class="row">
    <div class="col-md-12">
      <div class="card ">
        <!--#region watermark-->
        <div class="watermark" [hidden]="this.isWatermark === '0'">
          <h6>{{this.companyName}}</h6>
          <h6>{{this.userName}}</h6>
          <h6>{{this.sysDate | date}} {{this.currentTime}}</h6>
        </div>
        <!--#endregion watermark-->

        <div class="card-header ">
          <h5 class="card-title">Screen Name Info</h5>
          <div class="logo-image-small card-title col-md-2" [hidden]="this.userLog === ''">
            <img src="./assets/img/padlock.png" alt="alternative text" title="In Use by {{this.userLog}}"
              width="20px" />
          </div>
        </div>

        <div class="card-body ">
          <!--#region form-->
          <form #screennamedetailForm="ngForm"
            (ngSubmit)="onFormSubmit(screennamedetailForm.value, screennamedetailForm.valid)">

            <!--#region button submit dan back-->
            <button type="submit" class="btn btn-info btn-round" data-role="R00018520011852C"
              [hidden]="model.is_editable === false">
              Save
            </button>
            <button type="button" class="btn btn-danger btn-round" (click)="btnBack()">Back</button>
            <!--#endregion button submit dan back-->

            <!--#region row-->
            <div class="row">
              <!--#region code-->
              <div class="col-md-4">
                <div class="form-group">
                  <label for="code" class="control-label">Code *</label>
                  <input type="text" class="form-control" [ngModel]="model.code" #code="ngModel" name="p_code"
                    maxlength="10" required>
                  <small
                    *ngIf="code.errors?.required && code.touched || (code.errors?.required && screennamedetailForm.submitted) "
                    class="text-danger">
                    Code is required
                  </small>
                </div>
              </div>
              <!--#endregion code-->

              <!--#region name-->
              <div class="col-md-4">
                <div class="form-group">
                  <label for="name" class="control-label">Name *</label>
                  <input type="text" class="form-control" [ngModel]="model.name" #name="ngModel" name="p_name"
                    maxlength="250" required>
                  <small
                    *ngIf="name.errors?.required && name.touched || (name.errors?.required && screennamedetailForm.submitted) "
                    class="text-danger">
                    Name is required
                  </small>
                </div>
              </div>
              <!--#endregion name-->

              <!--#region type-->
              <div class="col-md-4">
                <div class="form-group">
                  <label>Type</label>
                  <select class='form-control col-md-6' [ngModel]="model.type" name="p_type" value='column'>
                    <option value='column'>Column</option>
                    <option value='pie'>Pie</option>
                    <option value='bar'>Bar</option>
                    <option value='line'>Line</option>
                    <option value='spline'>Spline</option>
                  </select>
                </div>
              </div>
              <!--#endregion type-->
            </div>
            <!--#endregion row-->

            <!--#region row-->
            <div class="row">
              <!--#region is_active-->
              <div class="col-md-4">
                <div class="form-check mt-3" style="margin-left: -35px;">
                  <div class="form-check">
                    <label class="form-check-label">
                      <input type="checkbox" class="form-check-input" [ngModel]="model.is_active" name="p_is_active">
                      Active
                      <span class="form-check-sign"></span>
                    </label>
                  </div>
                </div>
              </div>
              <!--#endregion is_active-->
            </div>
            <!--#endregion row-->
          </form>
          <!--#endregion form-->
        </div>
      </div>
    </div>
  </div>
</div>
```

### Screen function

**onFormSubmit()**

```Typescript
//#region  form submit
onFormSubmit(screennamedetailForm: NgForm, isValid: boolean) {
  // validation form submit
  if (!isValid) {
    swal({
      title: 'Warning',
      text: 'Please Fill a Mandatory Field OR Format Is Invalid',
      buttonsStyling: false,
      confirmButtonClass: 'btn btn-warning',
      type: 'warning'
    }).catch(swal.noop);

    return;
  } else {
    this.showSpinner = true;
  }

  this.screennamedetailData = screennamedetailForm;

  if (this.screennamedetailData.p_is_active == null) {
    this.screennamedetailData.p_is_active = false;
  }

  const usersJson: any[] = Array.of(this.screennamedetailData);

  if (this.param != null) {
    this.dalservice.Update(usersJson, this.APIController, this.APIRouteForUpdate)
      .subscribe(
        res => {
          this.showSpinner = false;
          const parse = JSON.parse(res);

          if (parse.result === 1) {
            this.showNotification('bottom', 'right', 'success');
            this.callGetrow();
          } else {
            this.swalPopUpMsg(parse.data);
          }
        },
        error => {
          this.showSpinner = false;
          const parse = JSON.parse(error);
          this.swalPopUpMsg(parse.data);
        });
  } else {
    this.dalservice.Insert(usersJson, this.APIController, this.APIRouteForInsert)
      .subscribe(
        res => {
          this.showSpinner = false;
          const parse = JSON.parse(res);

          if (parse.result === 1) {
            this.showNotification('bottom', 'right', 'success');
            this.route.navigate(['/path/to/edit/screen', parse.code]);
          } else {
            this.swalPopUpMsg(parse.data);
          }
        },
        error => {
          this.showSpinner = false;
          const parse = JSON.parse(error);
          this.swalPopUpMsg(parse.data);
        });
  }
}
//#endregion form submit
```

**callGetrow()**

```Typescript
//#region getrow data
callGetrow() {
  // param tambahan untuk getrow dynamic
  this.dataTamp = [{
    'p_code': this.param,
  }];
  // end param tambahan untuk getrow dynamic

  this.dalservice.Getrow(this.dataTamp, this.APIController, this.APIRouteForGetRow)
    .subscribe(
      res => {
        const parse = JSON.parse(res);
        const parsedata = this.getrowNgb(parse.data[0]);

        // checkbox
        if (parsedata.is_active === '1') {
          parsedata.is_active = true;
        } else {
          parsedata.is_active = false;
        }
        // end checkbox

        // mapper dbtoui
        Object.assign(this.model, parsedata);
        // end mapper dbtoui

        this.showSpinner = false;
      },
      error => {
        console.log('There was an error while Retrieving Data(API)' + error);
      });
}
//#endregion getrow data
```

**loadData()**

```Typescript

```

## Component

### Lookup

_Code snippet :_

```HTML
<!--#region lookup Field Field Name Code -->
<div class="col-md-4">
  <div class="form-group">
    <label class="control-label labelmodule">Field Field Name *</label>
    <div class="col-md-12 buttons">
      <span>
        <button
          type="button" class="btn btn-icon btn-outline-default btn-round btn-md col-md-4" (click)="btnlookupfieldname()" data-target="#lookupModalFieldField Name"
          data-toggle="modal" href="#">
          <i class="fa fa-search-plus"></i></button>
          <a>{{model.description}}</a>
        <input type="hidden" name="p_field name" [ngModel]="model.field name" required
          #field name="ngModel" />
        <input type="hidden" name="p_description" [ngModel]="model.description" />
      </span>
    </div>
    <small [hidden]="field name.valid || (field name.pristine && !formID.submitted)"
      class="text-danger">
      Field Field Name is required
    </small>
  </div>
</div>
<!--#endregion lookup Field Field Name field name -->
```

_Code snippet :_

```HTML
<!--#region modal lookup Field Field Name-->
<div class="modal bd-example-modal-lg" tabindex="-1" aria-hidden="true" id="lookupModalFieldField Name"
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
                  <td style="width: 20%; text-align: center;">{{ row.field name}}</td>
                  <td style="width: 80%;">{{ row.description }}</td>
                  <td class="text-center">
                    <button class="btn btn-outline-default btn-round"
                      (click)="btnSelectRowFieldField Name(row.field name,row.description)">Select</button>
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
<!--#endregion modal lookup Field Field Name-->
```

_Code snippet :_

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

btnSelectRowFieldField Name(field name: String, description: String) {
  this.model.field name = field name;
  this.model.description = description;

  $('#lookupModalFieldField Name').modal('hide');
}
//#endregion lookup field name
```

### IIFE (Immediately Invoked Function Expression)

_Code snippet :_

```Typescript
let J = 0, vm = this;

(function loopactivity() {
  if (J < vm.listData.length) {
    const field nameData = vm.listData[J];

    vm.dataTamp = [{
        'p_field name': field nameData
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

_Code snippet :_

```HTML
<button type="button" class="btn btn-success btn-round" data-role="[role-field name]" (click)="btnActivity(param)">Activity</button>
```

_Code snippet :_

```Typescript
//#region button Activity
btnActivity(field name: string) {
  // param tambahan
  this.dataRoleTamp = [{
    'p_field name': field name,
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

_Code snippet :_

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
