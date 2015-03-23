# anQular.js
Making angular.js and jQuery play nice.

(function ($, ng) {
    'use strict';

    var $val = $.fn.val; // save original jQuery function

    var recursivelyAssign = function (parentModel, elemNameArray, value) {
        var name = elemNameArray[0];

        if (elemNameArray.length === 1) {
            parentModel[name] = value;
            return;
        }

        var newArray = elemNameArray.slice(1);

        if (!parentModel[name])
            parentModel[name] = {};

        recursivelyAssign(parentModel[name], newArray, value);
    }

    // override jQuery function
    $.fn.val = function (value) {
        // if getter, just return original
        if (!arguments.length) {
            return $val.call(this);
        }

        // get result of original function
        var result = $val.call(this, value);

        var element = ng.element(this[0]);

        var scope = element.scope();

        var key = element.data('$ngModelController').$name;

        recursivelyAssign(scope.formModel, key.split('.'), value); // todo: dynamically get formModel, but leave for moment as too much other stuff to do

        scope.$apply();

        // return the original result
        return result;
    }
})(window.jQuery, window.angular);
