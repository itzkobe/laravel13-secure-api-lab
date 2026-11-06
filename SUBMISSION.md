# Submission: Laravel 13 Secure API Lab

- **Name:** Ervin Michael Kobe H. Besario
- **Section:** BSICT 3B
- **Repository:** https://github.com/itzkobe/laravel13-secure-api-lab

## Screenshots checklist

- [x] 01-public-api-200.png
- [x] 02-register-201.png
- [x] 03-login-200.png
- [x] 04-profile-no-token-401.png
- [x] 05-profile-with-token-200.png
- [x] 06-invalid-login-422.png
- [x] 07-logout-200.png
- [x] 08-revoked-token-401.png

## Reflection

A 401 response means the request had no valid token, so the API refused to return the profile. A successful profile response (200) happens when a valid Bearer token is sent, and Sanctum identifies the user and returns their id, name, and email. After logout the token is deleted, so using the same token again returns 401.