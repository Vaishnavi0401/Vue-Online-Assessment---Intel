# Vue Online Assessment - Intel  
 Enhancement of the Frontend Application using Vue.js 
Intel Online Assessment 

Tasks Completion

Progress :

      A. Initial Setup 
Installed VS Code, Node.js. 
Cloned the github repo 
On the terminal of the folder, installed npm 
On the terminal of the vs code, used npm run dev to serve the project on the localhost. 
Checked the functionality of the project - All seems to be working fine. 

      B. Migration from Vue 2 to Vue 3

npm uninstall vue - To uninstall the current version of Vue 2
npm install vite@^3.0.0 - To install the current version of Vue 3
npm install @vue/compat - The Vue’s migration build
In the package.json file, removing the dependencies of Vue 2
Replacing vue-template-compiler with @vue/compiler-sfc@^3.1.0
npm install @vitejs/plugin-vue@^3.2.0 --save -  To install the appropriate plug in
Resolved the dependency errors by changing the plugins from Vue 2 to Vue 3 in vite.config.js 
Checked the versioning of the vue using npm list vue
npm run dev - to serve the project


       C. Pagination 
Filtering out the status which are supposed to be hidden according to the user selection
Defining Page size and Page index functions 
Updating the Page size and Page index values
Slicing the data according to the Page size and Page index   
	
       D. CSS Styling 
Color Coding according to the statuses 
Adding CSS Properties to the tables, checkboxes and page updation

       E. Search Bar 
Implemented the text box and label.
Used v-model to bind the data 
Used .includes() function to check if the data contains even a part of the search term input from the user. 
Filtered the data accordingly 
Applied CSS Styling to the text-box and label.




Decisions :
  
Decided to use the npm install @vue/compat - The Vue’s migration build for migrating from Vue 2 to Vue 3 
Problems faced during Paginating the nested object, while keeping the track of Status and Core of the data. 
Decided to slice the data using .slice functionality on the array to split the data on the basis of hidden statuses. 
Problems faced during installation of the bootstrap modules for styling. 
Decided to manually style the components including color combinations and div locations 


Local Setup:


Install VS Code, Github Desktop(if required) and Node.js
Clone the github repo 
On the terminal of the folder, install npm 
On the terminal of the vs code, use npm run dev to serve the project on the localhost. 
If needed to publish to the local repository, create a git local repository and link it to the VS code repository
Git add . to create a branch. Commit and push to the repository. 





  
        






