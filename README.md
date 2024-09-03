## Dark Energy Spectrograph Instrument Legacy Imaging Surveys
本文主要介绍Dark Energy Spectrograph Instrument Legacy Imaging Surveys（DESI-LIS）及其数据细节和获取方式，帮助每一位想要挖掘DESI-LIS的用户更快地入门并访问海量的数据。DESI可以视为SDSS的继任者，其目标是在五年内拍摄14000平方度内三千万个星系的光谱。DESI-LIS是用来确定DESI拍摄光谱目标的测光巡天。为了在限定的三年时间内完成DESI-LIS巡天，其分为三个项目（DECaLS、BASS、MzLS）来进行grz三个波段，并加上了WISE卫星的四个红外波段观测。主要覆盖了如下的区域：
![](https://raw.githubusercontent.com/Rh-YE/Image/main/202409021631624.png)
### The Dark Energy Camera Legacy Survey (DECaLS) https://www.legacysurvey.org/decamls/
相机名：Dark Energy Camera (DECam)， 望远镜：Blanco 4m telescope。位于Cerro Tololo Inter-American Observatory。g和r波段在无月夜时拍摄，z波段在有月夜和早晨及黄昏时拍摄。
### The Beijing-Arizona Sky Survey (BASS) https://www.legacysurvey.org/bass/
相机名：90Prime，望远镜：Bok 2.3-m telescope。位于位于Kitt Peak National Observatory
在蓝端的响应更好，故BASS中g, r的效率更高。g波段在无月夜观测。
### Mayall z-band Legacy Survey (MzLS) https://www.legacysurvey.org/mzls/
相机名：MOSAIC-3，望远镜：4-meter Mayall telescope.位于Kitt Peak National Observatory。Mayall望远镜于1973年就开始运行，完成z-band巡天后于2018年2月12日关闭，开始安装DESI光谱仪。
### 总结
DESI主要分为south和north两个部分，北天为BASS+MzLS，南天为DECaLS，他们的一些参数如下。此外，BASS的分辨率为0.454 arcsec/pixel比DECaLS和MzLS的0.262arcsec/pixel要低。在DESI的pipeline中，BASS的分辨率会被插值到0.262arcsec/pixel。后文获取小图和大图数据时，也都默认是这个分辨率。

![](https://raw.githubusercontent.com/Rh-YE/Image/main/202409021633363.png)
严格意义上来说，南天不止有DECaLS，还有其他单位负责的在同一套设备上的其他巡天，如上文的覆盖区域图中标明的DES、DELVE

## 数据获取
DESI-LIS的图像主要有两种获取方式，大图模式和小图模式，大图模式的下载方式为整体下载或者根据brickname来定位brick确定大图。小图模式则是利用DESI提供的url模式，来调取DESI的自动裁剪程序，下载指定的图层。我们将分别举例说明这两种方式。
### 小图模式
DESI-LIS有一个交互式的网站,https://www.legacysurvey.org/viewer。我们可以通过在这个页面拖曳天区，在左下角输入坐标或目标名称来定位到对应位置，并根据右侧所选的图层展示对应的数据（右侧的overlays能够在显示图像以外，提供部分catalog信息）
通过点击画面中的任意位置,我们可以获取这个位置的赤经赤纬.
![](https://raw.githubusercontent.com/Rh-YE/Image/main/202409021918474.png)
点击Link Here获取该页面的链接,该链接打开后可以直接定位到当前位置.
点击Data，能显示该位置所属的所有拍摄文件等信息。包括每张图像的曝光时间，叠加的psf，误差图，没有重采样的图片
这种交互式的界面并不适合大批量获取数据，那么如何根据给定的RA Dec来下载数据?(ra dec在任何星表中都是会给的)
#### 通过wget下载定义好的url
DESI下载小图的一种常用方式是修改给定格式的url，然后使用wget下载这个url存储到指定位置.接下来我们会介绍在DESI-LIS中支持的所有天文图像类型及巡天的下载方式。在这个链接中有部分描述:
- https://www.legacysurvey.org/viewer/urls
- https://www.legacysurvey.org/dr10/description/#obtaining-images-and-raw-data
##### 叠加完的小图(最基础)
**提示：此方法下载得到的fits文件头文件不完整。**
`https://www.legacysurvey.org/viewer/cutout.{jpg or fits}?ra={RA}&dec={Dec}&layer={layer name}&pixscale={pixel scale}&size={size}&bands={bands}`支持下载给定坐标,图层,pixel scale和图像尺寸的jpg或fits图像.
其中，cutout.后面的部分输入jpg或者fits。size根据自己需求指定即可，目前DESI支持的最大size为512。更大的尺寸建议考虑切换至大图下载方式。**正如前文所述，DECaLS和MzLS的原始分辨率为0.262arcsec/pixel，BASS也已经在pipeline中被插值到这个大小，因此建议pixel scale设置为0.262，设置其他值网站会自动进行插值。**

layer控制了指定这个位置具体下载那个图层，常见的三种图为原图ls-dr9，model图ls-dr9-model，残差图ls-dr9-resid，由于north和south区域有一部分重叠,因此DESI支持添加ls-dr9-north-\*和ls-dr9-south-\*来选择调用那个巡天数据,如果不指定，则默认调用south数据。
layer支持还支持以下图层：`layer=['decals-dr5', 'decals-dr5-model', 'decals-dr7', 'decals-dr7-model','eboss','mzls+bass-dr6', 'mzls+bass-dr6-model','unwise-neo3', 'unwise-neo4', 'unwise-neo6', 'unwise-neo7','unwise-w3w4','unwise-cat-model','galex', 'wssa', 'des-dr1', 'hsc2', 'hsc-dr3','cfis-dr3-r', 'cfis-dr3-u','dr8-north', 'dr8-north-model', 'dr8-north-resid','dr8-south', 'dr8-south-model', 'dr8-south-resid','dr9c', 'dr9c-model', 'dr9c-resid','dr9d-south', 'dr9d-south-model', 'dr9d-south-resid','dr9d-north', 'dr9d-north-model', 'dr9d-north-resid','dr9e-south', 'dr9e-south-model', 'dr9e-south-resid','dr9e-north', 'dr9e-north-model', 'dr9e-north-resid','dr9sv-south', 'dr9sv-south-model', 'dr9sv-south-resid','dr9sv-north', 'dr9sv-north-model', 'dr9sv-north-resid','dr9sv', 'dr9sv-model', 'dr9sv-resid','fornax', 'fornax-model', 'fornax-resid','vlass1.2', 'ztf','ls-dr9-south', 'ls-dr9-south-model','ls-dr9-north', 'ls-dr9-north-model','ls-dr9.1.1', 'ls-dr9.1.1-model','ls-dr9-south-B', 'ls-dr9-south-B-model','asteroids-i','ls-dr10-early', 'ls-dr10a', 'ls-dr10a-model','ls-dr10', 'ls-dr10-model','ls-dr10-grz', 'ls-dr10-model-grz','pandas', 'wiro-C', 'wiro-D','suprime-L427', 'suprime-L427-model','suprime-L464', 'suprime-L464-model','suprime-L484', 'suprime-L484-model','suprime-L505','suprime-L505-model','suprime-L527', 'suprime-L527-model','suprime-ia-v1', 'suprime-ia-v1-model','cfht-cosmos-cahk','decaps2', 'decaps2-model','dr10-deep', 'dr10-deep-model', 'ibis-color', 'ibis',]`
如果不确定给定坐标是否有这些图层，请先使用python请求网站识别返回的状态码。该图层为源码中提取的：https://github.com/legacysurvey/imagine/blob/9e2aca9c0301e8f831aa1fa547ef005293c64ee6/render-tiles.py

当你定义完你的url，可以批量下载的方式是将所有的url写入一个文件（例如urls.txt)，里面的格式如下所示
```
with open("urls.txt", "w") as w:
	w.writelines(f"'{url}' {save path}\n")
```
注意url外面应当用单引号包裹，避免被错误解析。save path设置为需要保存的绝对路径。然后我们可以用下面的命令来并行下载文件。
```
nohup sh -c 'cat urls.txt | xargs -n 2 -P 64 sh -c '\''mkdir -p "$(dirname "$1")"; wget -t 5 -q -O "$1" "$0"'\''' &>/dev/null &
```
- **`nohup`**: 这个命令使得脚本即使在关闭终端后仍能继续运行。
- **`sh -c '...'`**: 通过调用 `sh -c`，传递了一段嵌套的命令。外层的单引号用来保护整个命令，避免被shell解析。
- **`cat /data1/DECaLS/invvar_urls_remained.txt`**: 使用 `cat` 命令读取文件 `/data1/DECaLS/invvar_urls_remained.txt` 的内容。
- **`xargs -n 2 -P 64`**: 这个命令从标准输入中获取数据并以两个参数为一组传递给后续的命令（`-n 2` 表示每次传递两个参数），并且同时启动 64 个并行进程（`-P 64`）。
- **`sh -c '\''...'`**: 内层的 `sh -c` 执行具体的命令，并且使用了单引号嵌套（`'\''`），以保护命令中的参数不被解析。    
- **`mkdir -p "$(dirname "$1")"`**: 为第一个参数 `$1` 创建其所在的目录结构，`$(dirname "$1")` 用于获取文件路径中的目录部分，`mkdir -p` 会递归创建缺少的目录。    
- **`wget -t 5 -q -O "$1" "$0"`**: 使用 `wget` 从 URL（由 `$0` 表示）下载文件，并将其保存到 `$1` 所指定的路径中。`-t 5` 表示最多重试 5 次，`-q` 表示静默模式（不输出下载过程信息），`-O "$1"` 表示将下载的内容保存到文件 `$1` 中。    
- **`&>/dev/null`**: 将所有输出（标准输出和标准错误）重定向到 `/dev/null`，即不显示任何输出信息。
- **`&`**: 将整个任务放入后台运行。
不建议并行进程数量-P超过64，如超过，下载失败的概率会显著提升，应该是被DESI-LIS服务器限制。
##### 下载psf
DESI-LIS提供了下载给定区域对应的点扩散函数PSF的url格式，这种方法在大图模式中貌似不能下载。仅限小图下载，并且下载的fits的data部分不是只有一个，而是hdu.data[0:3]分别对应grz波段。
```
f"https://www.legacysurvey.org/viewer/coadd-psf/?ra={RA}&dec={DEC}&layer={layer}"
```
##### 下载invvar误差图
误差图提供的是方差的倒数（inverse-variance），即1/sigma^2。在这里额外提及invvar是为了提醒以下内容
- 在当前版本的DESI-LIS数据下载逻辑中已经不支持单独下载invvar的小图，需要下载大图自己裁剪。
- 如果测光图像使用小图模式下载的，误差图下载的大图后再裁剪的，则两者的坐标容易出现对不齐的情况，建议需要误差图的情况下，测光图像也从大图中裁剪。
### 大图模式
DESI-LIS除了单张原图以外的所有科学图像，都在https://portal.nersc.gov/cfs/cosmo/data/legacysurvey/中根据不同的数据释放编号编排，https://www.legacysurvey.org/dr10/files/中提供了每个类型文件的具体描述。
这个链接内部是个文件夹的形式，直觉上我们可以直接递归下载整个数据集，但我推荐更快且更简单的方式是根据brickname索引，然后直接建立好所有的url，对于south区域只需要确定该brick是否有i-band数据即可。south或north区域包含的全部brickname都存在指定文件中，例如dr10的south区域则存在south/survey-bricks-dr10-south.fits.gz中。
```
from astropy.io import fits
brickname = fits.getdata("south/survey-bricks-dr10-south.fits.gz")["brickname"]
```
url内部的编排格式为（假设为南天区域且有i-band数据的情况）：
```
root = https://portal.nersc.gov/cfs/cosmo/data/legacysurvey/dr{data release num}/{south/north}/coadd/{brickname[:3]}/{brickname}
f"{root}//legacysurvey-{brickname}-blobmodel-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-blobmodel.jpg"
f"{root}/legacysurvey-{brickname}-ccds.fits"
f"{root}/legacysurvey-{brickname}-chi2-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-depth-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-depth.fits"
f"{root}/legacysurvey-{brickname}-galdepth-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-image-{W1/W2/W3/W4/g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-image.jpg"
f"{root}/legacysurvey-{brickname}-invvar-{W1/W2/W3/W4/g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-maskbits.fits.fz"
f"{root}/legacysurvey-{brickname}-model-{W1/W2/W3/W4/g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-model.jpg"
f"{root}/legacysurvey-{brickname}-nexp-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-psfsize-{g/r/i/z}.fits.fz"
f"{root}/legacysurvey-{brickname}-resid.jpg"
f"{root}/legacysurvey-{brickname}-wise.jpg"
f"{root}/legacysurvey-{brickname}-wisemodel.jpg"
f"{root}/legacysurvey-{brickname}-wiseresid.jpg"
f"{root}/legacysurvey_{data release num}_{south/north}_{coadd}_{brickname[:3]}_{brickname}.sha256sum"
```
#### 根据RA Dec定位brickname
下载大图通常是需要获取误差图大图并自己裁剪，因为DESI-LIS没有提供误差图的下载方式。此时，用户可能需要使用RA Dec来索引brickname从而进行下载。
```
from astropy.coordinates import SkyCoord
from astropy.io import fits
from astropy import units as u
def index_brickname(bricks_file, ra, dec):
    bricks = fits.getdata(bricks_file)
    ra1, ra2 = bricks["ra1"], bricks["ra2"]
    dec1, dec2 = bricks["dec1"], bricks["dec2"]
    coord = SkyCoord(ra=ra, dec=dec, unit=u.deg)
    ra_deg = coord.ra.deg
    dec_deg = coord.dec.deg
    mask = (ra_deg >= ra1) & (ra_deg <= ra2) & (dec_deg >= dec1) & (dec_deg <= dec2)
    matched_bricks = bricks[mask]
    if len(matched_bricks) > 0:
        return matched_bricks['brickname']
    else:
        return None
index_brickname("/datapool/DESI_LIS/dr10/south/survey-bricks-dr10-south.fits.gz", 194.3549, -17.6163)
```
#### 图像裁剪
```
class LISPipe():
    def __init__(self, img, invvar, ra, dec, wcs):
        self.ra = float(ra)
        self.dec = float(dec)
        self.img = img
        self.invvar = invvar
        self.wcs = wcs
        self.snr = self.img * np.sqrt(self.invvar)
    def fov_crop(self, img, h, w, center):
        return img[:, center[0, 1]-h//2:center[0, 1]+h//2, center[0, 0]-w//2:center[0, 0]+w//2]
    def crop_fov(self, h: int=128, w: int=128):
        pixel_coords = self.wcs.wcs_world2pix(np.column_stack((self.ra, self.dec)), 0).astype(int)
        self.img = self.fov_crop(self.img, h, w, pixel_coords)
        self.invvar = self.fov_crop(self.invvar, h, w, pixel_coords)
        self.snr = self.fov_crop(self.snr, h, w, pixel_coords)
        return self.img, self.invvar, self.snr


brickname = str(brickname)
img, invvar = [], []
wcs_path = os.path.join(coadd_root, f"{brickname[:-5]}", brickname, f"legacysurvey-{brickname}-image-g.fits.fz")
for band in ["g", "r", "z"]:
	coadd_img = os.path.join(coadd_root, f"{brickname[:-5]}", brickname, f"legacysurvey-{brickname}-image-{band}.fits.fz")
	img.append(fits.getdata(coadd_img))
	invvar_img = os.path.join(coadd_root, f"{brickname[:-5]}", brickname, f"legacysurvey-{brickname}-invvar-{band}.fits.fz")
	invvar.append(fits.getdata(invvar_img))
	img, invvar = np.array(img), np.array(invvar)
	hdu = fits.open(wcs_path)
	wcs = WCS(hdu[1].header)
# ra = 给定裁剪位置
# dec = 给定裁剪位置
target = LISPipe(img, invvar, ra, dec, wcs)
cropped_img, cropped_invvar, cropped_snr = target.crop_fov(h=128, w=128) # 设置裁剪大小
```
## Data
### 数据特性
DESI-LIS的叠加完的大图图像和小图图像的像素值单位都是nanomaggy，这是流量的常用表达方式，它可以通过以下方式与标准的星等表达方式联系：$m=22.5-2.5\log10(\text{flux})$。具体关于星等和流量的介绍可以参照SDSS的介绍：https://www.sdss3.org/dr8/algorithms/magnitudes.php
### Sweeps files
用于和SDSS的测光匹配，在https://www.legacysurvey.org/dr10/description/#photometry，提供了转换方式

## Update with issues
- DESI-LIS图像的物理方向与SDSS相反

## Citation
```
@misc{Ye2024,
title = {survey in DESI-LIS},
author = {Renhao Ye},
year = {2024},
url = {https://github.com/yourusername/yourrepository},
note = {survey in DESI, a guideline for understand DESI-LIS data and access},
```
or acknowledgment: 
```
We would like to thank Renhao Ye (叶人豪) for providing the "survey in DESI-LIS " on Github, which greatly assisted in our research.
```
## Acknowledgment
感谢DESI团队，感谢沈世银老师的指导，感谢陈宓、徐权峰、沈梦婷以及A3训练营中部分项目在处理DESI-LIS数据中所反馈的各种问题以促进此文档的形成。
## Reference
1. Dey, A. _et al._ Overview of the DESI Legacy Imaging Surveys. _The Astronomical Journal_ **157**, 168 (2019).
