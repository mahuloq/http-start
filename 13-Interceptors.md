# Interceptors are a service that runs on every thing sent

import {
HttpEvent,
HttpHandler,
HttpInterceptor,
HttpRequest,
} from "@angular/common/http";
import { Observable } from "rxjs";

export class AuthInterceptorService implements HttpInterceptor {
intercept(req: HttpRequest<any>, next: HttpHandler) {

    console.log("Request is on its way");
    return next.handle(req);

}
}

# You can also modify the request before they get sent with Interceptors

export class AuthInterceptorService implements HttpInterceptor {
intercept(req: HttpRequest<any>, next: HttpHandler) {
console.log("Request is on its way");

const modifiedRequest = req.clone({
headers: req.headers.append("Auth", "xyz"),
});
return next.handle(modifiedRequest);
}
}

# You can also modify the response

intercept(req: HttpRequest<any>, next: HttpHandler) {
console.log("Request is on its way");
const modifiedRequest = req.clone({
headers: req.headers.append("Auth", "xyz"),
});
return next.handle(modifiedRequest).pipe(
tap((event) => {
if (event.type === HttpEventType.Response) {
console.log("Response arrived, body data: ");
console.log(event.body);
}
})
);
}
}

# Can have multiple interceptors

export class LoggingInterceptorService implements HttpInterceptor {
intercept(req: HttpRequest<any>, next: HttpHandler) {
console.log("Outgoing request");
console.log(req.url);
console.log(req.headers);
return next.handle(req).pipe(
tap((event) => {
if (event.type === HttpEventType.Response) {
console.log(event.body);
}
})
);
}
}

# app module

providers: [
{
provide: HTTP_INTERCEPTORS,
useClass: AuthInterceptorService,
multi: true,
},
{
provide: HTTP_INTERCEPTORS,
useClass: LoggingInterceptorService,
multi: true,
},
],
