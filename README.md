Helpful Javascript Methods
===============

**1. Local Storage Decorator:**
```
function storage (...args) {
   if (args.length === 1) {
       return window.localStorage.getItem(args[0])
   } else if (args.length === 2) {
       return window.localStorage.setItem(args[0], args[1])
   }
}
```
**2. Calculate next id of array:**
```
function calculateNextId (arr) {
    let idCollection = arr.map((el) => el.id);
    let id = 0;

    for (let i = 0; i < arr.length; i++) {
        if (idCollection.indexOf(id) === -1) {
            return id;
        }
        id++
    }
    return id
}
```
**3. Check for IE browser:**
```
function isIeBrowser () {
  return /MSIE (\d+\.\d+);/.test(navigator.userAgent) || navigator.userAgent.indexOf("Trident/") > -1
}
```

**4. Validate smth:**
```
/// example: getValidationStatus('email', 'dsccds@ddv.com')

const validationSettings = {
    email: {
        isRequired: true,
        minLength: 5,
        maxLength: 25,
        errors: {
          empty: 'Please, fill the field',
          smallLength: 'Email too small',
          longLength: 'Email too long',
          falseValidation: 'Please, enter correct email'
        },
        successMessage: 'OK'
    },
    password: {
        isRequired: true,
        minLength: 5,
        maxLength: 30,
        errors: {
            empty: 'Please, enter the password',
            smallLength: 'Password too small',
            longLength: 'Password too long',
            noLowercase: function () {
                return 'Please, enter at least ' + validationSettings.password.minLowercaseLetters + ' lowercase letter';
            },
            noUppercase: function () {
              return 'Please, enter at least ' + validationSettings.password.minUpperCaseLetters + ' uppercase letter';
            },
            noNumbers: function () {
                return 'Please, enter at least ' + validationSettings.password.minNumbers + ' number';
            }
        },
        successMessage: 'OK',
        isLowercaseRequired: false,
        isUppercaseRequired: false,
        isNumberRequired: false,
        minLowercaseLetters: 1,
        minUpperCaseLetters: 1,
        minNumbers: 1
    },
    text: {
        isRequired: false,
        minLength: 5,
        maxLength: 25,
        errors: {
            empty: 'Please, fill the field',
            smallLength: 'Message too small',
            longLength: 'Message too long',
            falseValidation: 'Please, enter correct message'
        },
        successMessage: 'Ok'
    },
    phone: {
        isRequired: false,
        minLength: 4,
        maxLength: 30,
        errors: {
            empty: 'Please, enter telephone number',
            smallLength: 'Number too small',
            longLength: 'Number too long',
            falseValidation: 'Please, enter correct phone number'
        },
        successMessage: 'Ok'
    }
};

function getValidationStatus (dataType, formValue) {
    const val = formValue.trim();
    const status = {
      isValid: null,
      message: null
    };

    switch (dataType) {
        case 'email':
            return getEmailValidationStatus(val);
        case 'password':
            return getPasswordValidationStatus(val);
        case 'text':
            return getTextValidationStatus(val);
        case 'phone':
            return getPhoneValidationStatus(val);

    }

    function getEmailValidationStatus (val) {
        const settings = validationSettings.email;
        const emailReg = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;

        if (settings.isRequired && !val) {
          status.isValid = false;
          status.message = settings.errors.empty;
          return status
        }

        // check for length
        if (val.length < settings.minLength) {
            status.isValid = false;
            status.message = settings.errors.smallLength;
            return status
        }

        if (val.length > settings.maxLength) {
            status.isValid = false;
            status.message = settings.errors.longLength;
            return status
        }

        const isValid = val.match(emailReg);

        status.isValid = !!isValid;
        status.message = isValid ? settings.successMessage : settings.errors.falseValidation;

        return status
    }

    function getPasswordValidationStatus (val) {
        const settings = validationSettings.password;
        const upperCaseLettersReg = /[A-Z]/g;
        const lowerCaseLettersReg = /[a-z]/g;
        const numbersReg = /[0-9]/g;

        if (settings.isRequired && !val) {
            status.isValid = false;
            status.message = settings.errors.empty;
            return status
        }

        if (val.length < settings.minLength) {
            status.isValid = false;
            status.message = settings.errors.smallLength;
            return status
        }

        if (val.length > settings.maxLength) {
            status.isValid = false;
            status.message = settings.errors.longLength;
            return status
        }

        // optional check for lowercase, uppercase and numbers
        if (settings.isUppercaseRequired) {
            const uppercaseLetters = val.match(upperCaseLettersReg);
            if (!uppercaseLetters || uppercaseLetters.length < settings.minUpperCaseLetters) {
                status.isValid = false;
                status.message = settings.errors.noUppercase();
                return status
            }
        }

        if (settings.isLowercaseRequired) {
            const lowercaseLetters = val.match(lowerCaseLettersReg);
            if (!lowercaseLetters || lowercaseLetters.length < settings.minLowercaseLetters) {
                status.isValid = false;
                status.message = settings.errors.noLowercase();
                return status
            }
        }

        if (settings.isNumberRequired) {
            const numbers = val.match(numbersReg);
            if (!numbers || numbers.length < settings.minNumbers) {
                status.isValid = false;
                status.message = settings.errors.noNumbers();
                return status
            }
        }

        status.isValid = true;
        status.message = 'OK';
        return status
    }

    function getTextValidationStatus (val) {
        const settings = validationSettings.text;

        if (settings.isRequired && !val) {
            status.isValid = false;
            status.message = settings.errors.empty;
            return status
        }

        if (val.length < settings.minLength) {
            status.isValid = false;
            status.message = settings.errors.smallLength;
            return status
        }

        if (val.length > settings.maxLength) {
            status.isValid = false;
            status.message = settings.errors.longLength;
            return status
        }

        status.isValid = true;
        status.message = settings.successMessage;
        return status
    }

    function getPhoneValidationStatus (val) {
        const settings = validationSettings.phone;
        const phoneReg = /^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/;

        if (settings.isRequired && !val) {
            status.isValid = false;
            status.message = settings.errors.empty;
            return status
        }

        if (val.length < settings.minLength) {
            status.isValid = false;
            status.message = settings.errors.smallLength;
            return status
        }

        if (val.length > settings.maxLength) {
            status.isValid = false;
            status.message = settings.errors.longLength;
            return status
        }

        if (val.match(phoneReg)) {
            status.isValid = true;
            status.message = settings.message;
        } else {
            status.isValid = false;
            status.message = settings.errors.falseValidation
        }
        return status
    }

    return status;
}
```
**5. Check is img path exists:**
```
/// example: isImageAdressValid(src).then(() => {/*alright*/}).catch(() => {/*not alright*/});

isImageAdressValid (src) {
    const newImage = document.createElement('img');
    newImage.style.visibility = 'hidden';
    newImage.src = src;
    return new Promise(function (resolve, reject) {
        newImage.onload = function () {
            resolve()
        };
        newImage.onerror = function () {
            reject ()
        };
    });
}
```
**6.  Debounce function:**
``` usage: resize, scrolling
    activate func when event is ended
    
function debounce(func, wait, immediate) {
    var timeout;
    return function() {
        var context = this, args = arguments;
        var later = function() {
            timeout = null;
            if (!immediate) func.apply(context, args);
        };
        var callNow = immediate && !timeout;
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
        if (callNow) func.apply(context, args);
    };
};

   var myEfficientFn = debounce(function() {
             console.log('resize')
      }, 250);
   window.addEventListener('resize', myEfficientFn);
```

**7.  Once function:**
```
function once(fn, context) { 
	var result;
    
	return function() { 
		if(fn) {
			result = fn.apply(context || this, arguments);
			fn = null;
		}
        
		return result;
	};
}
 
// Example
var canOnlyFireOnce = once(function() {
	console.log('Запущено!');
});
 
canOnlyFireOnce(); // "Working"
canOnlyFireOnce(); // Not working
```

**8.  Throttle function:**
```
const throttle = (func, limit) => {
let lastFunc
let lastRan
return function() {
    const context = this
    const args = arguments
    if (!lastRan) {
	func.apply(context, args)
	lastRan = Date.now()
    } else {
	clearTimeout(lastFunc)
	lastFunc = setTimeout(function() {
	    if ((Date.now() - lastRan) >= limit) {
		func.apply(context, args)
		lastRan = Date.now()
	    }
	}, limit - (Date.now() - lastRan))
    }
}
}

var myEfficientFn = throttle(function() {
console.log('resize')
}, 1050);
window.addEventListener('resize', myEfficientFn);
```

**9.  Convert rgb(a) to hex color code:**
```
function rgb2hex(rgb){
    const color = rgb.match(/^rgba?[\s+]?\([\s+]?(\d+)[\s+]?,[\s+]?(\d+)[\s+]?,[\s+]?(\d+)[\s+]?/i);
    return (color && color.length === 4) ? "#" +
	("0" + parseInt(color[1],10).toString(16)).slice(-2) +
	("0" + parseInt(color[2],10).toString(16)).slice(-2) +
	("0" + parseInt(color[3],10).toString(16)).slice(-2) : '';
}
```

Sass functions (not js, but anyway)
===============

**1. Adaptive size:**
```
$browserContext: 16;
$baseWidth: 1000;
$baseHeight: 547;

@function em($pixels, $context: $browserContext) {
  @return #{$pixels/$context}em;
}

@function vh($target) {
  $vh-context: ($baseHeight*.01);
  @return #{$target/$vh-context}vh;
}

@function vw($target) {
  $vw-context: ($baseWidth*.01);
  @return #{$target/$vw-context}vw;
}
