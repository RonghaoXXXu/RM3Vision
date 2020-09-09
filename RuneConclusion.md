# Rune-Conclusion

- author:Ronghao-Xu

## Code

```c
class RuneDetector
{
public:
    {
        struct Point2fWithIdx;
        {
            Point2f p;
            size_t  idx;//soduku序号
            Point2fWithIdx();//对结构体赋值
        }

        typedef enum
        {
            RUNE_ORB,//特征点提取
            RUNE_GRAD,//edge检测
            RUNE_CANNY//边缘检测
        }Methed_Type;

        RuneDetector();//赋初值：(cellh,w->soduku)

        RotatedRect getRect(int idx);//获得特定Rect
        const RotatedRect &getRect(int idx) const;

        pair<int, int> getTarget(const cv::Mat & image);//获得目标
        {
            gray,threshold;
            //imshow
            findContous;
            //drawContours,imshow;
            初始化  vector<>sudoku_rects;
            if checkSudoku==True
            {
                if use_perspective==True
                    pair<int, int> idx = chooseTargetPerspective(src, sudoku_rects);
                else
                    pair<int, int> idx = chooseTarget(src, sudoku_rects);

            }
            return make_pair(-1,-1);
        }
    }
protected:
    {
        pair<int, int> chooseTargetPerspective(const cv::Mat & image,
                    const std::vector<cv::RotatedRect> & sudoku_rects);
        {
            vector<>centers->rect.center,Idex
            vector<>conrner->rect.points
            //sort以rect.center->y，x从小到大的顺序每三个排序
            // 0  1  2
            // 3  4  5
            // 6  7  8
            //coner_0268->获得每个[0,2,6,8] 的四个rect角
            rect=minAreaRect(coner_0268)->vertices//旋转矩形
            //sort以vertices.x从小到大排序
            //获得lu,ld,ru,rd
            //用dot距离最小->coner_0628里的真实点获得vertices
            //透视变换->矫正图像image->image_persp
            //##【看不懂perspective_mat】计算每个sudoku的平均w，h
            //##【看不懂cell_roi怎么得到的】获得每个sudoku的图片
            cell[9]
            int idx = -1;
            if (type == RUNE_ORB)
                idx = findTargetORB(cell);
            else if (type == RUNE_GRAD)
                idx = findTargetEdge(cell);
            else if (type = RUNE_CANNY)
                idx = findTargetCanny(cell)

            return pair
        }

        pair<int, int> chooseTarget(const cv::Mat & image,
                    const std::vector<cv::RotatedRect> & sudoku_rects);
        {
            //与chooseTargetPerspective类似
            //没有透视变换
        }
        int findTargetORB(cv::Mat * cells);
        {
            Mat descriptor[9]//9个cell的描述子
            vector<vector<>>Keypoints->resize(9)//9个特征点容器
            Ptr<ORB> orb=cv::ORB::create()
            BFMatch matcher(NORM_HAMMING,False)//False即普通审查
            for
            {
                desp,kp
                //检测每个cell的特征点并计算描述子：特征点附近的N个点对的亮度比较情况，是一种类似于二进制的编码
                    //描述子应该对光照（亮度）不敏感，具备尺度一致性（大小 ），旋转一致性（角度）等。
                for
                {   i>j，j++
                    //计算2种desp与descriptor[j]之间的匹配器对象
                    //计算出每对cell的匹配器对象数量->match_count
                }

            }
            // choose the minimun match cell as the target
            return cell的min_idx
        }

        int findTargetEdge(cv::Mat * cells);
        {

        }

        int findTargetCanny(cv::Mat * cells);

        RotatedRect adjustRect(const cv::RotatedRect & rect);
        {
            if rect.width>rect.height  return;
        }

        bool checkSudoku(const std::vector<std::vector<cv::Point2i> > & contours,
                    std::vector<cv::RotatedRect> & sudoku_rects);
        {
            vector<Point2f> centers;
            //筛选检验轮廓
            if contours.size < 9
                false
            if contours.size >= 9;
            {
                width,hight,low_thr,high_thr;
                RotatedRect rect ,;
                minAreaRect;//轮廓最小包裹矩形
                adjustRect //调整rect角度
                //一系列筛选，ratio，rect.angle;

                sudoku_rects.push_back(rect);
                centers.push_back(rect.center);
            }
            //筛选RotatedRect
            if sudoku_rects.size() >15
                false
            if sudoku_rects.size() <15 and >9
            {
                //初始化dist_map二维[i][j]：dist(indx_i与indx_j);
                //求距离
                //求中心大神符cell
                逐一比较每个神符中心到其余的距离和
                求最小，记下序号center_idx和最短距离和min_dist。
            }
            vector<pair<float//与中心神符的距离, int//序号> > dist_center;
            //sort三参数以距离由小到大排序，
            //temp就是转换介质符号
            //取前9个作为sudoku_rects
            return sudoku_rects.size() == 9;
        }

    }
private
    {
        std::vector<cv::RotatedRect> sudoku_rects;
        int sudoku_width;// pixel               127
        int sudoku_height;// piyel              71
        bool use_perspective;                   false
        Methed_Type type;                       RUNE_CANNY
        cv::Mat src;
    }
}
```

## Path

### 筛选得到正确Sudoku

1. camera->Image

2. 预处理（灰度二值化，阈值Rune thresold）->binary

3. 轮廓检测->contours

4. 筛选轮廓(checkSudoku)

> 1. contours.size >9 —> True
> 2. 拟合轮廓(minAreaRect) —> （adjustRect[x>y]）斜矩形rect
> 3. 通过长宽比,角度及长宽的阈值范围 筛选 —> Sudoku_rects [vector rect]; centers[vector rect.center]
> 4. Sudoku的数量 9 < num < 15 —>True —>*
> 5. 如果num==9，—> True 不需要进行*

#### *处理Sudoku得到有意义cells

> 1. 计算cells之间的距离 —> dist_map[i][j] —> min_dist 和 idx
> 2. 确定中心cell—> idx
> 3. 按从小到大排序中心cell到另外cells 的距离
> 4. 取最小的8个及中心cell 作为筛选好的神符矩形区域 —> Sudoku_rects
> 5. 如果 Sudoku_rects 的数量==9 —> True

### 获得九宫格(*透视变换)

1. 取每个cell的四个顶角,中心点及idx —> corners[vector Point2f] , centers[vector Point2fWithidx]

2. 按cell的中心点的y,x 均从小到大排序 得到0268的四个角 —> corner_0268

    | 0 | 1| 2 |
    | - | -| - |
    | 3 | 4| 5 |
    | 6 | 7| 8 |

3. 拟合corner_0268 —> rect —> 四个顶点vertices

4. 排序得到有顺序的四个顶点 lu,ld,ru,rd
    | 0 lu | 2 ru|
    | -    | -   |
    | 1 ld | 3 rd|

5. ***获得真实准确的九宫格的四个顶点—>_width,_hight***

> float v1 = (corners[i] - lu).dot((corners[i] - lu))  直接计算两个点的距离

#### *透视变换

1. src_p ; dst_p
    | 0 | 2 |
    | - | - |
    | 1 | 3 |

2. 利用image，perspective_mat 及_width,_hight —> image_persp

> 透视变换矩阵

### 投票机制
