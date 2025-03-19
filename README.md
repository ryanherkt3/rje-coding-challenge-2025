# CodingTask2025

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 15.2.1.

# Introduction
This repo contains a simple angular / ngrx project. The project consists of:
* Components - 
    The main structural elements of the app, this includes "Smart" components that interact with the store, and "Dumb" componenets that are only aware of their inputs and outputs.
* Dialogs - 
    Special componenets that make use of the Mat Dialog CDK, these are used for "User Input" effects.
* Models - 
    Define the shape of the data the app uses. (In our main codebase, this is where we would define out Zod schemas, for simplicity here we have just provided a simple type)
* Services - 
    The services are where we orchestrate interacting with the back end to perform CRUD operations, in this example we have provided some mock data and functions.
    This is also where we provide functions that launch the dialogs.
* State -
    The state folder contains the NGRX componenets for managing state.

In our main code base, you would expect to find this architecture replicated within each feature module that makes up the app. For simplicity here, we have included everything inside the app module.

# Your Mission
1. In the mock service, we have introduced an exaggerated delay when loading the contact list to simulate loading a larger data set into state. Extend the component, with the help of angular directives, to show a loading message when the contact list is empty.
2. We have the functionality to edit existing contacts, now add an "Add Contact" button to the contact list with the appropriate actions and effects. You should be able to re-use the contact-edit-dialog.
3. The effects deal with external interactions, as such there is no guarantee that they won't recieve an error response. How could we handle the case where a service function throws an error? You don't need to write any code for this one just, explain what you would change/add, optionally include an example snippet. (hint: rxjs provides an operator for this)
4. We have provided a Role type in the contact.model.ts file, assume a contact can be associated with one or many projects, and can have a different role on each. Explain with the help of diagrams what the interface for managing a contact's roles might look like and list the steps you would take to implement that feature. No code is required for this question, you can choose how you present this, either include it as a PDF in your repo, or provide a public link to a SAAS such as figma or whimsical.

# Answers

1. Modified `contact-list.component.html` to check the contact list has items (i.e. length > 0) - if true then the contact list is displayed, otherwise a loading message is displayed.

2.    
Added a new `addContactClicked` action and `launchAddDialog$` effect, which opens the `contact-edit-dialog` when the Add Contact button (added in `contact-list.component.html`) is clicked.

I modified the `contact-edit-dialog` component to show a different heading message depending on whether the user is editing a contact or adding one, and also edited the `saveContact` method in `contact.service.ts` to update the `contact` object with its proper ID whenever `index === -1` (as without this change, if trying to add a second new contact the details of the contact added previously will get updated instead).

3. If a service function throws an error, we can use the `catchError` operator provided by RxJS. Inside the operator, we can return an observable with information about what the cause of the error is.

Example snippet:
```
saveContact$ = createEffect(()=> this.actions$.pipe(
        ofType(actions.editContactConfrimed),
        concatMap(action =>
            this.contactService.saveContact$(action.contact).pipe(
                map(contact => actions.contactSavedSuccess({contact})),
                catchError((error: Error) => {
                    return of('Error saving contact: ' + error.message)
                })
            )
        )
    ))
```

4. https://www.figma.com/board/xzteWsZ6iJFcIvWToR7QQU/RJE-Coding-Challenge-Step-4---Ryan-Herkt