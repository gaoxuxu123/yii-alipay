
##安装
```
composer require yii-alipay/alipay dev-master

或者在composer.json中加入

 "require": {

        "yii-alipay/alipay": "dev-master"
}

```
更新依赖 ``` composer update ```

##使用说明

找到 `config/main.php` 文件 加入
```
 'components' => [

         'alipay'=>[

                     'class' =>'Yxu\\AlipayLib\\WebPay',
                     'partner' =>'xxxxxxxxxxxxxxxxxx',
                     'key' => 'xxxxxxxxxxxx',
                     'notify_url' =>'http://',//异步回调接口
                     'return_url' =>'http://'//同步回调接口
                 ],

 ]
```

##DEMO

###PC网页支付

```
/**
     * 提交支付的方法
     */
    public function actionPay()
    {
        //唯一的订单号
        $out_trade_no       = '20160122';
        //订单名称
        $subject            = 'xxxxxxxxxxx';
        //付款金额
        $total_fee          = '0.01';
        //订单描述
        $body               = 'xxxxxxxxxxx';
        //客户端ip
        $exter_invoke_ip    = Yii::$app->request->getUserIP();
        $parameter = array(
                            "exter_invoke_ip"=>$exter_invoke_ip,
                            "out_trade_no"	=> $out_trade_no,
                            "subject"	=> $subject,
                            "total_fee"	=> $total_fee,
                            "body"	=> $body,
                        );
        Yii::$app->alipay->buildRequestForm($parameter);
    }
    /**
     * 支付同步回调方法
     */
    public function actionReturn()
    {
        $verify_result  = Yii::$app->alipay->verifyReturn();
        if($verify_result){

            $parameter = [
                "out_trade_no"      => Yii::$app->request->get('out_trade_no'), //商户订单编号；
                "trade_no"          => Yii::$app->request->get('trade_no'),     //支付宝交易号；
                "total_fee"         =>  Yii::$app->request->get('total_fee'),    //交易金额；
                "trade_status"      =>  Yii::$app->request->get('trade_status'), //交易状态
                "notify_id"         =>  Yii::$app->request->get('notify_id'),    //通知校验ID。
                "notify_time"       =>  Yii::$app->request->get('notify_time'),  //通知的发送时间。格式为yyyy-MM-dd HH:mm:ss。
                "buyer_email"       =>  Yii::$app->request->get('buyer_email'),  //买家支付宝帐号；
            ];
            if( Yii::$app->request->get('trade_status') == 'TRADE_FINISHED' ||  Yii::$app->request->get('trade_status') == 'TRADE_SUCCESS') {
                //支付成功
                //处理订单状态，记录支付记录
                //检查是否已经支付 ,数据库交互
                //checkorderstatus();
                //修改支付状态
                //orderHandle();
                //跳转支付成功界面
                return $this->render('');
            }else{

                //跳转支付失败界面
                return $this->render('');
            }
        }else{
            //echo '支付失败!';
            return $this->render('');
        }
    }
    /**
     * 支付异步回调方法
     */
    public function actionNotify()
    {
        $verify_result  = Yii::$app->alipay->verifyNotify();
        $parameter = [
            "out_trade_no"      => Yii::$app->request->post('out_trade_no'), //商户订单编号；
            "trade_no"          => Yii::$app->request->post('trade_no'),     //支付宝交易号；
            "total_fee"         =>  Yii::$app->request->post('total_fee'),    //交易金额；
            "trade_status"      =>  Yii::$app->request->post('trade_status'), //交易状态
            "notify_id"         =>  Yii::$app->request->post('notify_id'),    //通知校验ID。
            "notify_time"       =>  Yii::$app->request->post('notify_time'),  //通知的发送时间。格式为yyyy-MM-dd HH:mm:ss。
            "buyer_email"       =>  Yii::$app->request->post('buyer_email'),  //买家支付宝帐号；
        ];
        if($verify_result){

            if(Yii::$app->request->post('trade_status') == 'TRADE_FINISHED') {
                //

            }else if (Yii::$app->request->post('trade_status') == 'TRADE_SUCCESS') {

                //进行订单处理，并传送从支付宝返回的参数；
                //检查是否已经支付,数据库交互
                //checkorderstatus();
                //修改支付状态
                //orderHandle();
            }
            //请不要修改或删除
            echo "success";

        }else{

            //验证失败
            echo "fail";
        }
    }

```
