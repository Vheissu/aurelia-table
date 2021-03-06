This is a fork of a fork. The [original plugin](https://github.com/tochoromero/aurelia-table) by Hector Romero is fantastic, however, it was missing server-side support (fetching and displaying data from an endpoint). So, [another developer forked it](https://github.com/jmvtrinidad/aurelia-table/) and added in support for server-side loading and submitted a pull request (which has and probably will never be merged).

And now, this is my fork. One of the features I desperately needed was the ability to disable the forward and back buttons with pagination, specifically when supplying your own pagination template via a replaceable part. I didn't want to allow a user to hammer an API by repeatedly clicking buttons, so this fork introduces some updates that include the ability to dynamically disable the buttons.

Please visit the [project page](http://tochoromero.github.com/aurelia-table) for the documentation and examples.

## Features

Aurelia Table is very easy to use, and you have complete control over the look and feel. You can make your table look exactly the way you want using plain html and css.
Out of the box you will get:

- Row Filtering
- Column Sorting
- Client side pagination
- Row Selection
- Server-side processing
- Ability to disable pagination buttons

## Getting started

1. Install package.

   ```
   npm i @jmvtrinidad/au-table --save
   ```

   or

   ```
   au install @jmvtrinidad/au-table
   ```

   then in your aurelia.json

   ```json
   {
     "dependencies": [
       {
         "name": "au-table",
         "main": "index",
         "path": "../node_modules/@jmvtrinidad/au-table/dist/amd",
         "resources": ["au-table-pagination.html"]
       }
     ]
   }
   ```

   and add this to your tsconfig.json

   ```json
   {
     "compilerOptions": {
       "baseUrl": "./",
       "paths": {
         "au-table": ["node_modules/@jmvtrinidad/au-table"]
       }
     }
   }
   ```

2. Specify your template.

   ```html
   <template>
     <div class="row">
       <div class="col-md-3">
         <form submit.delegate="tableSettings.loadItems()">
           <div class="input-group">
             <input
               type="text"
               class="form-control"
               id="exampleInputAmount"
               placeholder="Username"
               value.bind="tableSettings.filter.username"
             />
             <span class="input-group-btn">
               <button class="btn btn-default">Search</button>
             </span>
           </div>
         </form>
       </div>
       <div class="col-md-offset-6 col-md-3">
         <div class="form-inline">
           <div class="form-group pull-right">
             <label for="pageSize">Page Size: </label>
             <select
               value.bind="tableSettings.pageSize"
               id="pageSize"
               class="form-control"
             >
               <option model.bind="5">5</option>
               <option model.bind="10">10</option>
               <option model.bind="20">20</option>
               <option model.bind="50">50</option>
             </select>
           </div>
         </div>
       </div>
     </div>
     <table
       class="table table-striped"
       aurelia-table="data.bind: tableSettings.items; display-data.bind: $displayData; 
                                                         current-page.bind: tableSettings.currentPage; page-size.bind: tableSettings.pageSize;"
     >
       <thead>
         <tr>
           <th>Active</th>
           <th>Username</th>
           <th>Fullname</th>
           <th>Age</th>
           <th>Gender</th>
           <th>Birthdate</th>
         </tr>
       </thead>
       <tbody>
         <tr repeat.for="employee of $displayData">
           <td>${employee.userId}</td>
           <td>
             <a href="#" click.delegate="view(employee)">
               ${employee.username}
             </a>
           </td>
           <td>${employee.firstname + ' ' + employee.lastname}</td>
           <td>${employee.age}</td>
           <td>${employee.gender}</td>
           <td>${employee.birthdate | dateFormat}</td>
         </tr>
       </tbody>
     </table>
     <div class="row">
       <div class="col-md-9">
         <aut-pagination
           current-page.bind="tableSettings.currentPage"
           page-size.bind="tableSettings.pageSize"
           total-items.bind="tableSettings.totalItems"
           pagination-size.bind="5"
           boundary-links.bind="true"
         >
         </aut-pagination>
       </div>
     </div>
   </template>
   ```

3. Create your class.

   ```TypeScript
   // employee-list.ts
   import { EmployeeModal } from './employee-modal';
   import { TableSettings, TableResult } from 'au-table/au-table-settings';
   import { EmployeeService } from './employee-service';
   import { Employee } from './employee-model';

   export class EmployeeList {
     tableSettings = new TableSettings<Employee>(this.service.getEmployeeList.bind(this.service));

     static inject = [EmployeeService, DialogService];
     constructor(private service: EmployeeService, private dialogService: DialogService) {
     }

     bind() {
       return this.tableSettings.loadItems();
     }
   }

   // employee-service.ts
   import { TableSettings, TableResult } from 'au-table/au-table-settings';
   import { BaseHttpClient } from './../resources/index';
   import { Employee } from "./employee-model";

   export class EmployeeService {
       static inject = [BaseHttpClient]
       constructor(private client: BaseHttpClient) {

       }

       async getEmployeeList(request: ITableRequest<any>) {
           var result : TableResult < Employee > = {
             items: [],
             totalItems: 100,
             draw: 0
           }
           for (var index = 0; index < 10; index++) {
             result
               .items
               .push({
                 age: index,
                 birthdate: new Date().toString(),
                 firstname: 'jmv' + index,
                 gender: 'male',
                 isActive: true,
                 lastname: 'tri' + index,
                 userId: index,
                 username: 'jmvt' + index
               });
           }

           return result;
       }
   }

   ```

For a complete list of features and examples please visit the [project page](http://tochoromero.github.com/aurelia-table).
