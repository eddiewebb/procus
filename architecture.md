# Procus Technical Architecture

## Backend

We will use PHP with Laravel framework for all backend logic. Any features should be implemented to favor functionality offered by the framework, and stick to features compatible with the stated versions. 

- PHP 8.3
- Laravel 12

## Frontend

The frontend will be React components provided by Laravel Inertia library, ensuring clean integration to our Laravel backend, while offering modern JS frontend capabilities.

## Database

The codebase use of data repository must support an abstraction from the underlying database engine. While we will initially use MySQL or SQLlite as the supported engines, that list could grow in the future.  That means any migrations or schema should be agnostic to provider.

## Dependency Management

- Composer will manage the PHP depencies. 
