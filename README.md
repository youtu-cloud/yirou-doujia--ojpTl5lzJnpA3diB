
大家好，我是vzn呀，又见面了。


随着互联网在生活方方面面的应用，日常少不了要登录各个网站或者应用、或者是银行转账等需要验证自己身份的场景。从早期的输入账号密码来登录，到后来普遍开始通过手机验证码进行登录、或者APP扫码进行登录，身份校验的操作方式经历了一轮又一轮的迭代演进。


近年来，有越来越多的网站开始推广并引导用户启用所谓的**2FA**双重登录验证，比如Github就早在2022年的时候就开始引导用户启用2FA，近期还发出警告若用户在2024年10月22日前未启用2FA将被限制部分功能。而科技巨头Apple在最近发布的*IOS18*中，也亮相了全新的密码APP并提供对于2FA场景的临时鉴权码（IOS中称为`验证码`）生成的能力支持。


![](https://pics.codingcoder.cn/pics/202410191518073.png)


这个2FA究竟是何方神圣？为什么越发得到各大公司的青睐、甚至是Apple也要亲自入局？本篇文章我们就来一探究竟。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


## 初识2FA


所谓2FA，也即双因素认证（`Two-Factor Authentication`，简称2FA），是一种身份校验的策略。顾名思义，所谓双因素认证，就是你需要同时提供2方面的证据、来证明你就是你。


想要验证一个人是否为本人，用户提供的证据（认证因素）大体可分为3类：




| 分类 | 说明 | 举例 |
| --- | --- | --- |
| 私有秘密 | 一个私密的信息，仅本人可知晓的内容，知晓此内容即认为是合法的目标身份 | 最为常见，比如密码、密钥等 |
| 生理特征 | 基于人的各种独一无二的生理特征，来判断并确定是否为本人 | 比如人脸、指纹、声纹、虹膜等 |
| 专有物件 | 一个私人专属的物理设备或物件，持有此物件的人就是本人 | 身份证、手机、U盾等 |


任一认证因素都可以作为个人身份的认证，但又都有各自的缺点，无法做到100%可靠精准。


1. 使用账号密码登录： 存在密码泄露的风险。尤其是很多人喜欢所有账号都同一个密码，一旦泄露，后果不堪设想。
2. 使用人脸识别： 应用门槛高，需要硬件层面支持，web类应用难以应用。此外，生物特征属于不可变更类型，如果生物特征泄露将无法变更，后果比密码泄露更严重。
3. 使用U盾等专有物件：成本高、便捷度差，不仅要携带设备，一旦丢失还很麻烦。


所以，为了尽可能的提升认证结果的可信度，弥补单一认证因素存在的弊端，2FA认证方案应用而生。通过同时使用2种认证因素进行综合识别，来提升识别结果的可信度，保障身份认证的安全性。


话说到这里，既然单一认证有风险，2FA可以将风险降低，那为啥不直接搞个3FA呢？岂不是更加安全吗？这其实是软件实现中常见的一种取舍，毕竟软件最终是要服务于用户使用的，还是需要关注下用户使用的便捷度与使用体验，所以2FA相对而言，就是在安全和便捷之间取了个折中。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


## 2FA的形态演进


`2FA`并非是一个新鲜玩意，它很早就已经开始广泛应用在各种场景中了。随着时间的推移，其呈现形式也经历了数次的演进，服务接入门槛降低、用户使用的繁琐度也大幅下降。


下面举几个2FA的实际应用，感受下这些年2FA技术的变革。


* **早期网上银行的U盾**


早些年的时候，开通网上银行的时候，银行会提供一个类似U盘形状的`U盾`，或者是一个`密码生成器`（估计很多年轻小朋友都没见过，也算是时代的眼泪吧\~）。在需要转账的时候，除了要输入自己的银行卡号和取款密码，还需要将U盾插入到电脑上，或者用密码生成器生成一串数字，并将数字填入到网页中进行双重校验之后，才会允许转账操作。


![](https://pics.codingcoder.cn/pics/202409242226815.png)


这种2FA的应用场景中，分别使用了密码和独立物理设备进行综合认证。有效的规避了密码泄露或者U盾丢失带来的风险（当然，U盾和密码同时被另一个人拿到的话，就回天乏术了），保障个人资金的安全。


这种方式，虽然达到了账号安全性的要求，但是**弊端**也很明显：


1. 实施门槛高，需要生产配套物理设备，所以仅在银行这种财大气粗的行业领域中使用，很难在各行业中普遍推广。
2. 用户使用繁琐，如果设备不在身边或者丢失，则无法使用。而且，不同银行之间、甚至同一个银行的不同银行卡之间都有配套独立的设备，保存并区分也是一件很头疼的事情。


也是由于上述的原因，现在几乎已经看不到U盾的身影了。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


* **网站登录采用账密\+手机验证码方式**


这个是目前比较常见的一种2FA的应用形态。比如某度网盘，输入账号和密码验证通过后，还会要求向手机发送验证码，基于验证码进行二次身份认证通过之后，方可正常登录到系统中。


![](https://pics.codingcoder.cn/pics/202409242113560.png)


这种形态，其实算是早期的`密码+U盾`设备的一种升级方案。前面也说过了基于U盾等物理设备进行认证的成本与使用繁琐问题，而当前*手机*已经成为用户必备且几乎形影不离的物件，它便是U盾等设备的最佳替代品。


基于密码\+手机验证码的方式，在提升认证安全性的同时，打破了对特定配套物理设备的依赖，降低了2FA方案的落地成本与用户使用体验，被广泛的应用到了各种在线身份认证的场景中，成为了当前最为主流的一种2FA认证方式。


但是利用手机验证码进行验证，依旧会存在一个成本问题，毕竟发送短信也是要钱的，尤其是对于一些几亿用户体量的系统而言，即使每个用户1个月只发送一条短信，算下来也是一笔不菲的费用啊。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


* **iCloud网页版登录**


apple用户如果登录过网页版iCloud，应该都有见过iCloud的2FA实现思路，它在验证完用户的账号和密码之后，并非是发送短信验证码，而是向登录了此账号的iphone设备推送了一条弹窗通知，里面显示了一串随机码，用户输入iPhone接收到的随机码，完成身份验证并进入到系统重。


![](https://pics.codingcoder.cn/pics/202409241336784.png)


![](https://pics.codingcoder.cn/pics/202409241337155.png)


苹果的这种实现，借助自身iPhone设备的广泛应用，构建了服务端与iPhone设备之间的专有推送通道，完美的省掉了短信验证码发送的费用。但，这种方案，就像网上很多人调侃苹果的那句`Only Apple Can Do`一样，还真的只有Apple等手机设备厂商可以实现。对于普通的系统服务提供者，想要通过非短信途径推送验证码给用户，也至少得要用户在手机中安装个自己产品的APP应用，才有可能实现利用自己的通道进行点对点消息推送，但这一条件显然限制了该方案的推行。


总体而言，iCloud的这种做法，是一种更加经济的2FA方案，但是技术门槛与推广门槛极高，不具备普遍性。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


* **GitHub网站登录**


并不是所有公司都是手机厂商。所以是否有一种通用的、成本更低廉的2FA实现方案呢？在这个背景下，一种基于`TOTP`协议的2FA方案进入大众视野中。当前很多启用2FA的网站，使用的都是这一方案。


看下GitHub的2FA登录实现。在开启2FA功能的时候，需要在提前在手机上安装一个APP并绑定到GitHub账号上。这样后续在输入账号密码之后，还需要打开APP并将APP中生成的鉴权码填入到界面上进行二次验证，验证通过之后方可进入系统。


![](https://pics.codingcoder.cn/pics/202409241327385.png)


![](https://pics.codingcoder.cn/pics/202409241331128.png)


这里的手机上安装的APP，也是基于TOTP协议进行开发的密钥生成器。这一方案接入成本相对较低、更容易推广，目前正在逐步被各类系统所支持。值得一提的是，正如本文开头提及的消息，在9月中旬刚刚发布的IOS18系统中自带了一款名为密码的APP，其中提供了一个验证码功能，也正是基于TOTP协议的鉴权码生成器，使用它生成的验证码也可以正常完成2FA认证。


![](https://pics.codingcoder.cn/pics/202409252307074.png)


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


## 基于TOTP的2FA


在上面介绍GitHub的2FA方案的时候，有提过其采用的是基于`TOTP算法`的2FA方案。所谓TOTP，即基于时间的一次性密码（`Time-based One-Time Password`，简称TOTP），它是一种国际标准协议（`RFC6238`）。其本质上就是取当前时间戳以及当前账号的一个唯一标识（类似密钥，服务端颁发、并提供给客户端保存使用），通过固定算法加工生成一个6位数的鉴权码，一定时间范围内生成的鉴权码是固定的（所以这个验证码会有个有效期的概念，一般是30s）。这样只要服务端和APP端各自计算出一个验证码，然后比对下两个验证码是否一致，即可完成校验。



```

def generate_totp(secret, interval=30, digits=6, timestamp=None):
    """
    生成基于时间的一次性密码（TOTP）。

    :param secret: 密钥（base32 编码的字符串）
    :param interval: 时间间隔（秒）
    :param digits: 生成的 OTP 的位数
    :param timestamp: 当前时间戳（秒），默认为当前时间
    :return: 生成的 OTP 字符串
    """
    if timestamp is None:
        timestamp = int(time.time())

    # 计算时间步长（时间戳除以时间间隔并取整）
    counter = timestamp // interval

    # 将时间步长转换为字节序列
    counter_bytes = struct.pack('>Q', counter)

    # 使用 HMAC-SHA1 生成哈希值
    hmac_result = hmac.new(base64.b32decode(secret), counter_bytes, hashlib.sha1).digest()

    # 动态截断以生成 OTP
    offset = hmac_result[-1] & 0xf
    binary_otp = hmac_result[offset:offset + 4]
    binary_otp = struct.unpack('>I', b'\x00' + binary_otp[1:3] + b'\x00')[0]

    # 将 OTP 格式化为指定位数的字符串
    otp = str(binary_otp % 10**digits).zfill(digits)

    return otp

def main():
    # 示例密钥（base32 编码）
    secret = 'JBSWY3DPEHPK3PXP'

    # 生成当前时间的 TOTP
    otp = generate_totp(secret)
    print(f"Generated TOTP: {otp}")

    # 验证生成的 OTP（假设客户端和服务器的时间同步）
    verified_otp = input("Enter the OTP you received: ")
    if otp == verified_otp:
        print("Verification successful!")
    else:
        print("Verification failed!")

if __name__ == "__main__":
    main()


```

基于上面介绍，可以看出，基于TOTP算法生成验证码有两个输入因子：时间和用户密钥。服务端和客户端除了需使用相同的加密算法，还需要保证传入相同的时间戳和用户密钥，才能保证生成的校验码相同。如何保证服务端和客户端设备，可以获取到相同的参数值呢？下面简单介绍下。


* 时间因子


服务端和客户端在计算生成验证码的时候，各自取自身设备本地当前时间作为时间参数。因为如今的智能手机和服务器都支持基于网络的时钟校准能力，所以可以很轻松的保证手机终端与应用服务端本地时间的基本一致。


![](https://pics.codingcoder.cn/pics/202409252352262.png)


* 用户密钥因子


用户密钥因子是服务端为用户生成的授权密钥，计算生成验证码的时候，服务端和客户端都要使用同一个密钥进行计算，所以服务端为用户生成密钥后，除了服务端要保存该密钥与用户的绑定关系，还需要将此密钥提供给用户、由用户将其绑定到手机上的TOTP软件中（所谓绑定，本质上就是将服务端生成的密钥存储到手机的TOTP软件中）。绑定完成后，服务端和客户端就都拥有相同的用户密钥信息了。


![](https://pics.codingcoder.cn/pics/202410191558126.png)


至此，TOTP算法所需的2个关键参数都已具备，就可以使用TOTP应用生成的验证码进行身份二次认证咯。


讲到这里，小伙伴们可能会有个疑惑，假如用户更换了新手机，新手机上安装的TOTP软件并没有绑定对应的用户密钥信息，那不就没法登录了吗？


这就要再回到开启2FA认证的时候，服务端除了会生成一个密钥（类似公钥）提供给客户端进行绑定，还会同时提供一份`Recovery Code`，会提示用户将其可靠保存起来。


![](https://pics.codingcoder.cn/pics/202409252212920.png)


目前市面上主流的基于TOTP的客户端软件，主要有2个：


* Google Authenticator
* MicroSoft Authenticator


当然，现在又多了个`Apple Password`，以后可能会形成三足鼎立的局面。


![](https://pics.codingcoder.cn/pics/202408181107713.gif)


## 服务中集成2FA能力


如果需要在服务端开启2FA能力，需要集成实现对应的TOTP密钥算法即可。以JAVA为例，可以通过集成现有的第三方库来快捷实现，常用的有`com.warrenstrange.googleauth`库：



```
<dependency>
    <groupId>com.warrenstrangegroupId>
    <artifactId>googleauthartifactId>
    <version>{version}version>
dependency>

```

代码中直接使用其提供的api接口即可，下面代码演示下api接口的使用方式：



```
import com.warrenstrange.googleauth.GoogleAuthenticator;
import com.warrenstrange.googleauth.IKey;
import com.warrenstrange.googleauth.KeyGenerator;

import java.time.Instant;

public class TOTPExample {
    public static void main(String[] args) {
        // 生成一个密钥、可以指定密钥长度，例如20字节
        IKey key = KeyGenerator.getKey(20); 
        String secretKey = key.getKey();
        // 计算当前时间的时间窗口
        long currentTime = Instant.now().getEpochSecond();
        int timeStep = 30; 
        long timeWindow = currentTime / timeStep;
        // 生成TOTP
        GoogleAuthenticator ga = new GoogleAuthenticator();
        String totp = ga.getTotpPassword(secretKey, timeWindow);
        System.out.println("Generated TOTP: " + totp);

        // 校验验证码是否符合预期
        boolean isValid = ga.authorize(totp, secretKey, timeWindow);
        System.out.println("TOTP Validation: " + (isValid ? "Success!" : "Failed!"));

        // 考虑到网络延迟等因素，可以允许一定的时间误差。例如，验证时允许前后一个时间窗口的误差
        boolean isValidWithTolerance = false;
        for (int i = -1; i <= 1; i++) {
            long toleranceWindow = timeWindow + i;
            if (ga.authorize(totp, secretKey, toleranceWindow)) {
                isValidWithTolerance = true;
                break;
            }
        }
        System.out.println("TOTP Validation with Tolerance: " + (isValidWithTolerance ? "Success!" : "Failed!"));
    }
}

```

![](https://pics.codingcoder.cn/pics/202408181107713.gif)


## 总结


好咯，关于双因素认证的内容，就聊到这里，相信小伙伴们对2FA已经有所了解了吧。随着互联网时代对个人信息与隐私的日益重视、以及现今网络安全形势的日益严峻，用户身份认证的技术方案与实现形态也在不断地发展与优化，相信2FA在后续也会得到更大的普及。关于2FA或者用户身份验证的方案，小伙伴们是否有自己的见解呢，欢迎留言交流。


![](https://pics.codingcoder.cn/pics/202408181055031.gif)


**我是vzn呀，聊技术、又不仅仅聊技术\~**


如果觉得有用，请点个关注，也可以关注下我的公众号【是vzn呀】，获取更及时的更新。


期待与你一起探讨，一起成长为更好的自己。


![](https://pics.codingcoder.cn/pics/202408181051237.jpg)


 本博客参考[豆荚加速器](https://yirou.org)。转载请注明出处！
