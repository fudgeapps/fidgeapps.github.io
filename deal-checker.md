---
title: Deal Checker - Compare supermarket deals to get the best value 
---
<!DOCTYPE html>
<html lang="en-GB" itemscope itemtype="http://schema.org/Article" manifest="cache.manifest">
<head>
	<meta charset="UTF-8">
	<meta content="IE=edge,chrome=1" name="X-UA-Compatible">
	<meta name="viewport" content="width=device-width">
	<meta name="mobile-web-app-capable" content="yes">
	<title>Deal Checker - Compare supermarket deals to get the best value</title>
	<link href="https://fonts.googleapis.com/css?family=Roboto:400,300,700" rel="stylesheet">
	<link href="/assets/css/bootplus.min.css" rel="stylesheet">
	<link href="/assets/css/bootplus-responsive.min.css" rel="stylesheet">
	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.1.min.js"></script>
	<script src="https://maxcdn.bootstrapcdn.com/twitter-bootstrap/2.3.2/js/bootstrap.min.js"></script>
	<script src="https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.2.1.js"></script>
	<script src="https://ssl.google-analytics.com/ga.js" async></script>
	<script>
        var _gaq = _gaq || [];
        _gaq.push(['_setAccount', 'UA-19491723-3']);
        _gaq.push(['_trackPageview']);
        var Deal = function (data) {
            "use strict";
            var self = this;
            this.deal = ko.observable(data.deal);
            this.stdQty = ko.observable(data.stdQty);
            this.specialRate = ko.observable(data.specialRate);
            this.specialQty = ko.observable(data.specialQty);
            this.price = ko.observable(data.price);
            this.unitSize = ko.observable(data.unitSize);
            this.unitMeasure = ko.observable(data.unitMeasure);

            this.setDeal = function (d) { self.deal(d); };

            this.totalSize = ko.computed(function () {
                var size = this.unitSize();
                if (this.unitMeasure() === 'ml' || this.unitMeasure() === 'g') { size = size / 1000; }
                return size * (Number(this.stdQty()) + Number(this.specialQty()));
            }, this);

            this.totalPrice = ko.computed(function () {
                switch (Number(this.deal())) {
                    case 1:
                        return ((this.price() * this.stdQty()) + ((this.price() * this.specialQty()) * this.specialRate())).toFixed(2);
                    case 2:
                        return (Number(this.price())).toFixed(2);
                }
            }, this);

            this.dealText = ko.computed(function () {
                switch (Number(this.deal())) {
                    case 1:
                        return "Buy " + this.stdQty() + " get " + this.specialQty() + " " + this.specialRateText();
                    case 2:
                        return "Buy " + this.stdQty() + " for &pound;" + Number(this.price()).toFixed(2);
                }
                return '';
            }, this);

            this.specialRateText = ko.computed(function () {
                switch (Number(this.specialRate())) {
                    case 0:
                        return "free";
                    case 0.5:
                        return "half price";
                }
            }, this);

            this.largeMeasurePrice = ko.computed(function () {
                var p = (this.totalPrice() / this.totalSize()).toFixed(2);
                if (isNaN(p)) { return; }
                return p;
            }, this);

            this.smallMeasurePrice = ko.computed(function () {
                var p = (this.largeMeasurePrice() / 10).toFixed(2);
                if (isNaN(p)) { return; }
                return p;
            }, this);

            this.smallMeasureUnit = ko.computed(function () {
                switch (this.unitMeasure()) {
                    case "g":
                    case "kg":
                        return "g";
                    case "ml":
                    case "l":
                        return "ml";
                }
            }, this);

            this.largeMeasureUnit = ko.computed(function () {
                switch (this.unitMeasure()) {
                    case "g":
                    case "kg":
                        return "kg";
                    case "ml":
                    case "l":
                        return "litre";
                    case "items":
                        return "item";
                }
            }, this);

            this.offerprice = ko.computed(function () {
                var p = (this.totalPrice() / (Number(this.stdQty()) + Number(this.specialQty()))).toFixed(2);
                if (this.unitMeasure() === "items") {
                    p = (p / this.unitSize()).toFixed(2);
                }
                return p;
            }, this);

            this.deal.subscribe(function (newValue) { if (newValue === '2') { self.specialQty(0); } });
        };

        var DealsViewModel = function () {
            "use strict";
            var self = this;
            self.deals = ko.observableArray([]);
            self.addDeal = function () { self.deals.push(new Deal({ stdQty: 1, specialQty: 1, price: 0, calculated: true })); };
            self.removeDeal = function (deal) { self.deals.remove(deal); };
        };
        var dvm;
        $(function () {
            "use strict";
            dvm = new DealsViewModel();
            dvm.addDeal();
            ko.applyBindings(dvm);
            $('#newDeal').on('click', function () { dvm.addDeal(); });
            $(document).on('focus', 'input[type="number"]', function () { var e = $(this), val = e.val(); if (val !== '') { e.data('val', val); e.val(''); } }).on('blur', 'input[type="number"]', function () { var e = $(this), val = e.val(); if (val === '') { e.val(e.data('val')); } });
        });
    </script>
    <style>
        .input-append select.add-on {
            min-height: 31px;
            padding: 0;
            background: #fff;
        }
        .spacer {
            margin-bottom: 10px;
        }
        select.input-medium {
            min-height: 31px;
        }
    </style>
    <meta itemprop="name" content="Deal Checker - Compare supermarket deals to get the best value">
    <meta itemprop="description" content="Which is better? Two 1.35 litre bottles of orange juice for &pound;4.50 or one 900ml bottle at &pound;1.09? Use this calculator to compare supermarket deals and find out which one is the best value.">
</head>
<body>
    <div id="fb-root"></div>
    <script>
        (function(d, s, id) {
        var js, fjs = d.getElementsByTagName(s)[0];
        if (d.getElementById(id)) return;
        js = d.createElement(s); js.id = id;
        js.src = "https://connect.facebook.net/en_GB/all.js#xfbml=1&status=0";
        fjs.parentNode.insertBefore(js, fjs);
        }(document, 'script', 'facebook-jssdk'));
    </script>
    <div class="container">
        <div class="row">
            <div class="span12">
                <h1>Deal Checker</h1>
                <p class="lead">Which is better? Two 1.35 litre bottles of orange juice for &pound;4.50 or one 900ml bottle at &pound;1.09? Use this calculator to compare supermarket deals and find out which one is the best value.</p>
                <p data-bind="visible: deals().length === 0"><button class="btn btn-primary" id="newDeal">Check a deal</button></p>
            </div>
        </div>
        
        <div class="row-fluid" id="calc">
            <div class="span12" data-bind="foreach: deals, visible: deals().length > 0">
            
                <div class="card">
                    <header>
                        <h3 class="card-heading simple">Deal <span data-bind="text: $index() + 1"></span></h3>
                    </header>
                    <div class="card-body">
                        <form class="row-fluid">
                            <div class="span6 form-inline">
                                <h4>Deal Type</h4>
                                <div class="spacer focus-select">
                                    <label class="radio"><input type="radio" name="deal" value="1" data-bind="checked: deal">Buy</label> <input type="number" class="input-mini" step="1" min="0" value="1" name="stdQty" data-bind="value: stdQty" required> get 
                                    <input type="number" class="input-mini" step="1" min="0" value="1" name="specialqty" data-bind="value: specialQty" required> 
                                    <select class="input-medium" name="specialrate" data-bind="value: specialRate">
                                            <option value="0">free</option>
                                            <option value="0.5">half-price</option>
                                        </select>
                                </div>
                                <div class="focus-select">
                                    <label class="radio"><input type="radio" name="deal" value="2" data-bind="checked: deal">Buy</label> <input type="number" class="input-mini" step="1" min="0" value="2" name="stdQty" data-bind="value: stdQty" required> for <div class="input-prepend"><span class="add-on">&pound;</span><input class="input-mini" type="number" min="0" step="0.01" required name="specialprice" data-bind="value: price"></div>
                                </div>
                            </div>

                            <div class="span6">
                                <h4>Item Details</h4>
                                <div>Please enter the details of <strong>one</strong> item or pack.</div>
                                <div data-bind="visible: deal() === '1'">
                                    <div class="input-prepend"><span class="add-on">&pound;</span><input class="input-medium" type="number" placeholder="Cost of one" min="0" step="0.01" required name="price" data-bind="value: price"></div>
                                </div>
                                
                                <div>
                                    <div class="input-append">
                                        <input class="input-medium" type="number" placeholder="Enter pack size, weight, or volume of one" min="0" step="0.01" required name="unitsize" data-bind="value: unitSize">
                                        <select class="add-on" required name="unitmeasure" data-bind="value: unitMeasure">
                                            <option></option>
                                            <optgroup label="Multipack">
                                                <option>items</option>
                                            </optgroup>
                                            <optgroup label="Volume">
                                                <option>ml</option>
                                                <option>l</option>
                                            </optgroup>
                                            <optgroup label="Weight">
                                                <option>g</option>
                                                <option>kg</option>
                                            </optgroup>
                                        </select>
                                    </div>
                                </div>
                            </div>
                        </form>
                        <div class="row-fluid" data-bind="visible: dealText() != ''">
                            <div class="span6">
                                <div><strong>Offer:</strong> <span data-bind="html: dealText"></span></div>
                                <div data-bind="visible: !isNaN(totalPrice())"><strong>Total Price:</strong> &pound;<span data-bind="text: totalPrice"></span></div>
                                <div data-bind="visible: !isNaN(totalSize())"><strong>Total Size:</strong> <span data-bind="text: totalSize"></span> <span data-bind="text: largeMeasureUnit"></span>(s)</div>
                            </div class="span6">
                            <div class="span6">
                                <div data-bind="visible: unitMeasure() != ''"><strong>Price per item:</strong> &pound;<span data-bind="text: offerprice"></span></div>
                                <div data-bind="visible: unitMeasure() != '' && unitMeasure() != 'items'"><strong>Price per 100<span data-bind="text: smallMeasureUnit"></span>:</strong> &pound;<span data-bind="text: smallMeasurePrice"></span></div>
                                <div data-bind="visible: unitMeasure() != '' && unitMeasure() != 'items'"><strong>Price per <span data-bind="text: largeMeasureUnit"></span>:</strong> &pound;<span data-bind="text: largeMeasurePrice"></span></div>
                            </div>
                        </div>
                    </div>
                    <div class="card-actions">
                        <button class="btn btn-primary" data-bind="click: $parent.addDeal">Check another deal</button>
                        <button class="btn btn-danger" data-bind="click: $parent.removeDeal">Remove this deal</button>
                    </div>
                </div>
            </div>
        </div>
        <hr>
        <footer>
            <div>Designed, developed and &copy; 2013 <a href="https://ben.cheetham.me.uk/" title="Produced by Ben Cheetham">Ben Cheetham</a>
            <div class="pull-right"><div class="g-plus" data-action="share" data-width="200"></div>
            <a href="https://twitter.com/share" class="twitter-share-button" data-url="https://www.fudgeapps.uk/deal-checker">Tweet</a>
            <div class="fb-like" data-href="https://www.fudgeapps.uk/deal-checker" data-send="true" data-width="225" data-show-faces="false"></div>
            </div>
            </div>	
        </footer>
    </div>

    <script>
    !function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');

    window.___gcfg = {lang: 'en-GB'};

    (function() {
        var po = document.createElement('script'); po.async = true;
        po.src = 'https://apis.google.com/js/plusone.js';
        var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
    })();
    </script>
</body>
</html>