# Form Validation

Racoon provides flexible way to validate your forms. You need to import fields from `racoon::forms::fields` module.

The available fields are:

- InputField
    - InputField&lt;String&gt;
    - InputField&lt;Option&lt;String&gt;&gt;
    - InputField&lt;Vec&lt;String&gt;&gt;
    - InputField&lt;Option&lt;Vec&lt;String&gt;&gt;

- FileField
    - FileField&lt;UploadedFile&gt;
    - FileField&lt;Option&lt;UploadedFile&gt;&gt;
    - FileField&lt;Vec&lt;UploadedFile&gt;&gt;
    - FileField&lt;Option&lt;Vec<UploadedFile&gt;&gt;&gt;

- UuidField
    - UuidField&lt;Uuid&gt;
    - UuidField&lt;Option&lt;Uuid&gt;&gt;
    - UuidField&lt;Vec&lt;Uuid&gt;&gt;
    - UuidField&lt;Option&lt;Uuid&gt;&gt;

## Define form fields

Create new struct and define your form fields.

```rust
use racoon::forms::fields::file_field::{FileField, UploadedFile};
use racoon::forms::fields::input_field::InputField;
use racoon::forms::FormFields;

struct UploadForm {
    name: InputField<String>,
    address: InputField<Option<String>>,
    profile_photo: FileField<UploadedFile>,
}
```

## Implement FormValidator trait

The form field takes `field_name` argument. 

```rust
impl FormValidator for UploadForm {
  fn new() -> Self {
    let name = InputField::new("name");
    let address = InputField::new("address");
    let profile_photo = FileField::new("profile");

    Self {
        name,
        address,
        profile_photo,
    }
  }

  fn form_fields(&mut self) -> FormFields {
    // List here form fields
    vec![
        self.name.wrap(),
        self.address.wrap(),
        self.profile_photo.wrap(),
    ]
  }
}
```

## Using in view
```rust
async fn upload_profile(request: Request) -> Response {
    let form = UploadForm::new();
    match form.validate(&request).await {
        Ok(form) => {
            // Printing form values
            println!("Name: {}", form.name.value().await);
            println!("Address: {:?}", form.address.value().await);
            println!("File Name: {}", form.profile_photo.value().await.filename);
            return HttpResponse::ok().body("Upload success");
        }
        Err(error) => {
            return HttpResponse::bad_request().body(format!("{:?}", error.field_errors));
        }
    }
}
```

## Custom validation

Add your custom validation logic inside the `post_validate()` method. It will only be called after field is validated with default constraints.
Some fields may not have `post_validate()` method.

```rust
let name: InputField<String> = InputField::new("name").post_validate(|value| {
  if value.eq("default") {
     return Err(vec!["Default value is selected".to_string()]);
  }
  return Ok(value);
});
```

## Custom errors

You can customize the error messages with`handle_error_message()` method.

```rust
let name: InputField<String> = InputField::new("name").handle_error_message(|error, default_errors| {
  match error {
    InputFieldError::MissingField(field_name) => {
      println!("Missing field name: {}", field_name);
      return vec!["Custom field missing error".to_string()];
    }
    _ => {}
  }
  
  // Returns default error messages
  default_errors
});
```
