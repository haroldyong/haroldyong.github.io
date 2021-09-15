### practise in webchat dev


## 1. 账户体系


1. 用户唯一性

为了识别用户，每个用户针对每个公众号会产生一个安全的OpenID，如果需要在多公众号、移动应用之间做用户共通，则需前往微信开放平台，将这些公众号和应用绑定到一个开放平台账号下，绑定后，一个用户虽然对多个公众号和应用有多个不同的OpenID，但他对所有这些同一开放平台账号下的公众号和应用，只有一个UnionID。
如果开发者拥有多个移动应用、网站应用、和公众帐号（包括小程序），可通过 UnionID 来区分用户的唯一性，因为只要是同一个微信开放平台帐号下的移动应用、网站应用和公众帐号（包括小程序），用户的 UnionID 是唯一的。换句话说，同一用户，对同一个微信开放平台下的不同应用，UnionID是相同的

参考 https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html

openid,普通用户的标识，对当前开发者帐号唯一;
unionid,用户统一标识。针对一个微信开放平台帐号下的应用，同一用户的unionid是唯一的。
建议：开发者最好保存用户unionID信息，以便以后在不同应用中进行用户信息互通。
https://developers.weixin.qq.com/doc/oplatform/Website_App/WeChat_Login/Authorized_Interface_Calling_UnionID.html
