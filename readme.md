welcome to hashtagbime's api
in this readme file i will guide you through creating, submitting, paying, and receiving the insurance file
the process of releasing an insurance in the app is different from what ad to give you so this version is dedicated to SAMAN and the client's who support their insurance

MAIN URL: "87.107.135.248:3000"

1. creating a new health request:
    when creating a health request we have three concepts:
    a. the health request: is the health request
    b. the main sub request: is the personal information of the main user.
    c. sub requests
    the process:
    a. the creation of the health request:
        in this part we create a health request and create one sub request as a main sub request
        `{
            URL:"/samanAPI/HealthRequests/BasicInformation/",
            Method: 'POST',
            Headers:{
                        Client-Key: the client key that were gavin to you in the previews document,
                        },  
            body:{
                            "birthday": "1993-03-16" [value type(date), required(true), persian(تاریخ تولد)],
                            "subRequestCount": 1 [value type(integer), required(true), persian(تعداد درخواست های زیر مجموعه)],
                            "phone": "09381434789" [value type(string(11 || 13(+98))), required(), persian(شماره موبایل)],
                            "stateCode": 1 [value type(integer), required(true), persian(کد استان)],
                            "cityCode": 2832 [value type(integer), required(true), persian(کد شهر)],
                            "insurance": "تامین اجتماعی" [value type(string), required(true), persian(دفترچه ی بیمه)],
                            "weight": 65 [value type(integer), required(true), persian(وزن)],
                            "height": 175 [value type(integer), required(true), persian(قد)],
                           },
            response = {
                           "success": true,
                           "data": {
                               "UserToken": "23ac50a916bf" [value type(string), persian(شناسه  ی کاربر)],
                               "requestID": 456 [value type(integer), persian(ای دی درخواست ساخته شده)]
                           }
                       }
        }`
    2. in this part we create the sub requests if exist (if the subRequestCount in the last post were 0 you don't need to send this post)
        `{
            URL:"/samanAPI/HealthRequests/createSubRequests/",
            Method: 'POST'
            Headers:{
                                Client-Key: the client key that were gavin to you in the previews document,
                                Authorization : user token given in the creation request
                      },
            body:{
                           "requestID": 457 [value type(integer), required(true), persian(شناسه ی درخواست اصلی)],
                           "subRequests": [{
                                 "birthday": "1993-03-16" [value type(date), required(true), persian(تاریخ تولد)],
                                 "weight": 56 [value type(integer), required(true), persian(وزن)],
                                 "height":168 [value type(), required(), persian(قد)],
                                }]
                           },
            response: {
                                "success": true,
                                "message": "sub requests successfully added"
                                }
        }`
    3. selecting health offer
        `{
                URL: "/SamanAPI/HealthRequests/SelectHealthOffer",
                Method: 'GET',
                Headers: {
                            Client-Key: the client key that were gavin to you in the previews document,
                            Authorization : user token given in the creation request
                            },
                Params: {
                    offerID: the id of the offer the user selected,
                    requestID: the main health request id
                },
                response: {
                    "success": true,
                    "message": "health offer successfully saved"
                }
        }`
    4. submit the health forms:
        `{
            URL:"/SamanAPI/HealthRequests/SubmitSubRequest",
            Method: 'POST',
            Headers:{
                                        Client-Key: the client key that were gavin to you in the previews document,
                                        Authorization : user token given in the creation request
                              }
            body:{
                "requestID" : 458 ,
                "subRequestID" : 284 ,
                .... (items received from /SamanAPI/Statistics/HealthFormList method read more in the statistics section)
            }
            response: {
                                "success": true,
                                "message": "successfully submitted"
                            }
        }`                    
        response codeStatus on success = 200
        Note1: notice that you need to post these information for each sub request including the main sub request
        Note2: in this method if the field is required it should be send like firstName and lastName (as mentioned in the questions list in /SamanAPI/Statistics/HealthFormList method response), otherwise if the field is filled include it in the
        post body in other words just send the yeses and filled more fields.
        Note3: in case the main question in a set is set to 1 at least one of the subSet questions must be true or sent in case it is a string. 
    5. uploading the user's files:
        for each health sub request you need to upload three pictures in base64 format
        Note: the pictures should be submitted before the payment, if the user tries to resubmit the pictures after the payment is done the resposne will have code 409 conflict
        a. uploading national card picture:
            `{
                URL:"/SamanAPI/FileControl/uploadNationalCard",
                Method: 'POST',
                Headers:{
                                            Client-Key: the client key that were gavin to you in the previews document,
                                            Authorization : user token given in the creation request
                                  },
                params = {
                    requestID: request id (required),
                    subRequestID: sub request id (required)
                },
                body:{
                    file: (file name = 'NationalCard')
                },
                response: {
                                    "success": true,
                                    "message": "successfully uploaded"
                                },
            }`                        
            response success status code = 201
            
        b. uploading base insurance card picture:
            `{
                URL:"/SamanAPI/FileControl/uploadInsuranceCard",
                Method: 'POST',
                Headers:{
                                            Client-Key: the client key that were gavin to you in the previews document,
                                            Authorization : user token given in the creation request
                                  },
                params = {
                    requestID: request id (required),
                    subRequestID: sub request id (required)
                },
                body:{
                    file: (file name = 'InsuranceCard')
                },
                response: {
                                    "success": true,
                                    "message": "successfully uploaded"
                                }
            }`                    
            response success status code = 201
        c. uploading birth certificate first page picture
            `{    
                 URL:"/SamanAPI/FileControl/uploadBirthCertificate",
                 Method: 'POST',
                 Headers:{
                                             Client-Key: the client key that were gavin to you in the previews document,
                                             Authorization : user token given in the creation request
                                   },
                 params = {
                    requestID: request id (required),
                    subRequestID: sub request id (required)
                },
                body:{
                    file: (file name = 'NationalCard')
                },
                response: {
                                    "success": true,
                                    "message": "successfully uploaded"
                                }
            }`                    
            response success status code = 201  
        Note: notice that the user should upoad 2 pictures for every sub request that he created, and insurance card picture if exists
        d. uploading general test (if requested by the doctor)
            `{    
                 URL:"/SamanAPI/FileControl/uploadGeneralTest",
                 Method: 'POST',
                 Headers:{
                                             Client-Key: the client key that were gavin to you in the previews document,
                                             Authorization : user token given in the creation request
                                   },
                params = {
                    requestID: request id (required),
                    subRequestID: sub request id (required)
                },
                body:{
                    file: (file name = 'GeneralTest')
                },
                response: {
                                    "success": true,
                                    "message": "successfully uploaded"
                                }
            }`                    
            response success status code = 201
5. downloading the released insurance file 
    when the agent releases the insurance file the user downloads the file as a picture in base64 format:
    `{
        URL:"/SamanAPI/FileControl/InsuranceDownload",
        Method: 'GET',
        Headers:{
                                    Client-Key: the client key that were gavin to you in the previews document,
                                    Authorization : user token given in the creation request
                          },
        Parameters: {
                        "subRequestID": the sub request id
                     },
        response: {
                            file: (te insurance file as blob)
                        }
    }`                             
6. statistics routes:
    this part of the api helps you get reports about the requests and sub requests:
    a. get the health offers for the health request
        `{
            URL:"/SamanAPI/Statistics/healthOffers"
            Method: 'GET',
            Headers:{
                        Client-Key: the client key that were gavin to you in the previews document,
                        Authorization : user token given in the creation request
                    },
            Parameters: {
                            requestID: the request id
                          },
            response: {
                                "success": true,
                                "data": [
                                    {
                                        "OfferID": 1,
                                        "CompanyName": "سامان",
                                        "name": (health offer's persian name)
                                        "DentalSupport": "100000" پوشش دندانپزشکی,
                                        "SurgerySupportA": "6000000" پوشش جراحی های مهم,
                                        "MedicineSupport": "100000" ویزیت و دارو,
                                        "PregnancySupport": "1500000" پوشش زایمانی,
                                        "MTestSupport": "100000" ام تی تست,
                                        "ParaClinicSupportA": "300000" پوشش پاراکلینیک گروه اول,
                                        "ParaClinicSupportB": "150000"پوشش پاراکلینیک گروه دوم,
                                        "HospitalSupport": "3000000" بیمارستان,
                                        "SurgerySupportB": "150000" جراحی مجاز یرپایی,
                                        "AmbulanceSupport": "200000" امبولانس شهری و بین شهری,
                                        "ageA": 250000 هزینه ی نفری در بازه سنی 0-15,
                                        "ageB": 500000 هزینه ی نفری در بازه سنی 15-50,
                                        "ageC": 600000 هزینه ی نفری در بازه سنی 50-60,
                                        "ageD": 750000 هزینه ی نفری در بازه سنی بیشتر از 60 سالگی,
                                        "totalPrice": 500000 هزینه ی کل گرح با توجه به تعداد نفرات و بازه سنیشان و داشتن دفترچه بیمه
                                    },....
                                ]
                            },
        }`                    
        response status code on success = 200                
        Note: notice that in order to get this information you need to create all the sub requests
    b. get the selected health offer information for the health request
            `{
                URL:"/SamanAPI/Statistics/SelectedHealthOffer",
                Method: 'GET',
                Headers:{
                                            Client-Key: the client key that were gavin to you in the previews document,
                                            Authorization : user token given in the creation request
                                  },
                Parameters: {
                                requestID: the request id
                              },
                response: {
                                    "success": true,
                                    "data": {
                                        "OfferID": 1,
                                            "CompanyName": "سامان",
                                            "DentalSupport": "100000" پوشش دندانپزشکی,
                                            "SurgerySupportA": "6000000" پوشش جراحی های مهم,
                                            "MedicineSupport": "100000" ویزیت و دارو,
                                            "PregnancySupport": "1500000" پوشش زایمانی,
                                            "MTestSupport": "100000" ام تی تست,
                                            "ParaClinicSupportA": "300000" پوشش پاراکلینیک گروه اول,
                                            "ParaClinicSupportB": "150000"پوشش پاراکلینیک گروه دوم,
                                            "HospitalSupport": "3000000" بیمارستان,
                                            "SurgerySupportB": "150000" جراحی مجاز یرپایی,
                                            "AmbulanceSupport": "200000" امبولانس شهری و بین شهری,
                                            "totalPrice": 500000 هزینه ی کل گرح با توجه به تعداد نفرات و بازه سنیشان و داشتن دفترچه بیمه
                                    }
                                }
            }`                                                                                        
                response status code on success = 200       
        c. get a list of all the health requests created by the ueser
            `{    
                URL:"/SamanAPI/Statistics/HealthRequestsList",
                Method: 'GET',
                Headers:{
                                            Client-Key: the client key that were gavin to you in the previews document,
                                            Authorization : user token given in the creation request
                                  },
                response: {
                                    "success": true,
                                    "data": [
                                        {
                                            "RequestID": "360"
                                        },
                                        {
                                            "RequestID": "377"
                                        },
                                        {
                                            "RequestID": "379"
                                        }
                                    ]              
                                }
            }`
            response status code on success = 200
        d. get status of the health request:
            this route returns the status you need about the health request
            `{    
                URL:"/SamanAPI/Statistics/GetSubRequestsStatus",
                Method: 'GET',
                Headers:{
                                            Client-Key: the client key that were gavin to you in the previews document,
                                            Authorization : user token given in the creation request
                                  },
                Parameters: {
                                requestID: the health request's id
                            },       
                response: {
                                     "success": true,
                                     "data": [
                                         {
                                             "subRequestID": 291,
                                             "age": "26",
                                             "Weight": "65",
                                             "Height": "175",
                                             "DateOfCreation": "2019-05-03T19:30:00.000Z",
                                             "TimeOfCreation": "05:24:24",
                                             "FormsSubmission": 1 [if the user submitted the forms value is 1 otherwise 0],
                                             "doctorReplyStatus": "accepted" [the doctor's reply: {accepted, rejected, test, onHold}],
                                             "paid": false [returns true if the payment is done],
                                             "NationalCard": true [returns true if the user uploaded the NationalCard image],
                                             "BirthCertificate": true [returns true if the user uploaded the BirthCertificate image],
                                             "InsuranceCard": true [returns true if the user uploaded the InsuranceCard image],
                                             "HealthOffer": "1" [the id of the health offer selected for the health request],
                                             "MainRequest": true [returns true if the sub request is the main subRequest(belongs to the main user)],
                                             "newInsurance": true [returns true if the insurance was released and ready to be downloaded]
                                         }, ....
                                     ]
                                 }
            }`                     
             response status code on success = 200
        e. get users requests (by phone number)
            in this route you can send the phone number of the user that where sent to the server in basicinfo and get all the requests he/she made
            `{    
                URL: "/samanAPI/Statistics/HealthRequestsLisByPhoneNumber",
                Method: "GET"
                Headers:{
                                                Client-Key: the client key that were gavin to you in the previews document
                                      },
                Params: {
                    phone: the user's phone number
                },
                response: {
                                    "success": true,
                                    "data": {
                                        "userToken": the user token that where saved in the system,
                                        "list": the list that contains the request ids of all the health requests the user created
                                        [
                                            {
                                                "RequestID": "495"
                                            },...
                                        ]
                                    }
                                }
            }`                    
        f. get health form qestions list
            `{
                URL: "/samanAPI/Statistics/HealthFormList",
                Method: "GET",
                Headers:{
                                                Client-Key: the client key that were gavin to you in the previews document
                                      },
                response: {
                                    "success": true,
                                    "data":{
                                        "questions": [
                                            {
                                                "id": questions id,
                                                "persianLabel": the persian label of the question which the user should see in the frontend app,
                                                "englishLabel": the english name of the question (used as an index in the sent array when submitting the form),
                                                "required": has to values "YES" in case requeired "NO" otherwise,
                                                "description": description about the question if needed,
                                                "dataType": data types are mentiond few lines later,
                                                "parentQuestionID": if a question is a subset of another question this field will have the id of the said question
                                            }, ....
                                        ]
                                    }
                                }
            }`                
            P.S 
                1: dataTypes:
                    varchar: a string
                    varchar(number): astring with a specific number of characters
                    ['item1', 'item2', ..., 'itemN']: list of the specific values allowed as an answer to the question
                    number: an number
                    number(number): number with specific length
                    TINYINT: 0 or 1 as in yes or no
                    text: long string (used for description and "more" like textArray)
                2: when filling the health form in case of having a set of questions (parentQuestionID is not null) if the parent is 1 then at least one of the subset questions must have a value (1 or a text depends on the filled subset type)

5. payment process:
    whenever the user request's for payment the api will return an invoice:
    the payment process is in two fazes
    a. requesting the payment page and amount
        `{
            URL:"/samanAPI/Payment/inVoice",
            Method: 'GET'
            Headers:{
                                        Client-Key: the client key that were gavin to you in the previews document,
                                        Authorization : user token given in the creation request
                              }
            Parameters: {
                requestID = the id of the health request
            }
            response: {
                                "success": true,
                                "data": {
                                    "res_num": factor number (integer),
                                    "amount": 100 (final total price in toman),
                                    "subRequestsIDList": the list of the sub request id's that are ready to be paid and where calculated in the process of calculating total price
                                    [
                                        {
                                            "subRequestID": 329
                                        },
                                        {
                                            "subRequestID": 330
                                        }
                                    ]
                                }
                            }
            response status code on success = 200
        }`    
    b. approving the payment
        `{
            URL:"/samanAPI/Payment/callback",
            Method: 'POST',
            Headers:{
                                        Client-Key: the client key that were gavin to you in the previews document,
                                        Authorization : user token given in the creation request
                              },
            params = {
                res_num: شناسه فاکتور,
                msisdn: شماره موبای کاربر,
                ref_num: شماره ارجاع پرداخت,
                trace_num: شماره پیگیری پرداخت,
                amount: مبلغ پرداخت شده
            },
            response: {
                               "success": true,
                               "message": "تراکنش با موفقیت انجام شد"
                           },
        }`
        response status code on success = 200
                                       
response codes:
-	200 OK: Successful
-	201 Created: Created
-	400 Bad Request: Bad input parameter. Error message should indicate which one and why.
-	401 Unauthorized: The client passed in the invalid Auth token. Client should refresh the token and then try again.
-	403 Forbidden: * Customer doesn’t exist. * Application not registered. * Application try to access to properties not belong to an App. * Application try to trash/purge root node. * Application try to update contentProperties. * Operation is blocked (for third-party apps). * Customer account over quota.
-	404 Not Found: Resource not found.
-	405 Method Not Allowed: The resource doesn't support the specified HTTP verb.
-	409 Conflict: Conflict.
-	500 Internal Server Error: Servers are not working as expected. The request is probably valid but needs to be requested again later.
-	503 Service Unavailable: Service Unavailable
                          