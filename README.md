## Api Postman tests for restful-booker
https://restful-booker.herokuapp.com/apidoc/index.html

### Here are some curls and api tests via Postman.

For example, we have this curl:
```
curl --location 'https://restful-booker.herokuapp.com/booking' \
--header 'Content-Type: application/json' \
--data '{
"firstname" : "Jim",
"lastname" : "Brown",
"totalprice" : 111,
"depositpaid" : true,
"bookingdates" : {
"checkin" : "2018-01-01",
"checkout" : "2019-01-01"
},
"additionalneeds" : "Breakfast"
}'
```
To test it, we can do this:
```
pm.test("Status code is 200", () => {
pm.response.to.have.status(200);
});

// Define the expected JSON schema
const expectedSchema = {
"type": "object",
"properties": {
"bookingid": { "type": "number" }
},
"required": ["bookingid"]
};

// Validate the response against the expected schema
pm.test("Response has the correct JSON schema", () => {
const Ajv = require("ajv");
const ajv = new Ajv();
const valid = ajv.validate(expectedSchema, pm.response.json());

    if (!valid) {
        pm.expect(valid, ajv.errorsText(ajv.errors));
    }
});

//Check if specific values in the response match the expected values
pm.test("Check response values", () => {
const responseBody = pm.response.json();
pm.expect(responseBody.booking.firstname).to.eql("Jim");
pm.expect(responseBody.booking.lastname).to.eql("Brown");
pm.expect(responseBody.booking.totalprice).to.eql(111);
pm.expect(responseBody.booking.depositpaid).to.be.true;
pm.expect(responseBody.booking.bookingdates.checkin).to.eql("2018-01-01");
pm.expect(responseBody.booking.bookingdates.checkout).to.eql("2019-01-01");
pm.expect(responseBody.booking.additionalneeds).to.eql("Breakfast");
});
```