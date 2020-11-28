
{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "name": "youtbe-adview-prediction.ipynb",
      "provenance": [],
      "toc_visible": true,
      "authorship_tag": "ABX9TyM1EtrGNRmwksGexG8+MLXv",
      "include_colab_link": true
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    }
  },
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/Karnak123/youtube-adview-prediction/blob/main/youtbe_adview_prediction.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "k5F9o-A0XJfU"
      },
      "source": [
        "# Import relevant libraries"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "NN2v7TMRVNo_"
      },
      "source": [
        "import datetime\n",
        "import time\n",
        "import pandas as pd\n",
        "import numpy as np\n",
        "import matplotlib.cm as cm\n",
        "import matplotlib.pyplot as plt\n",
        "import seaborn as sns\n",
        "import joblib\n",
        "import keras\n",
        "from sklearn.preprocessing import LabelEncoder, MinMaxScaler\n",
        "from sklearn.model_selection import train_test_split\n",
        "from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score\n",
        "from sklearn.linear_model import LinearRegression\n",
        "from sklearn.svm import SVR\n",
        "from sklearn.tree import DecisionTreeRegressor\n",
        "from sklearn.ensemble import RandomForestRegressor\n",
        "from sklearn.model_selection import RandomizedSearchCV\n",
        "from google.colab import files"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "MPKyx6AxXQrt"
      },
      "source": [
        "# Read csv"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "21U3jAvp_eBM"
      },
      "source": [
        "Manually upload file after downloading from https://drive.google.com/file/d/1Dv-HF10AUUA03AO_cQvar462eXawk0iQ/view"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Eo-FUFokWLuQ"
      },
      "source": [
        "data = pd.read_csv('train.csv')\n",
        "test = pd.read_csv('test.csv')"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 402
        },
        "id": "wLU2Dk6Gir0j",
        "outputId": "cb8e7c6a-cf1a-475a-b8e9-53155030e4d4"
      },
      "source": [
        "data"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>vidid</th>\n",
              "      <th>adview</th>\n",
              "      <th>views</th>\n",
              "      <th>likes</th>\n",
              "      <th>dislikes</th>\n",
              "      <th>comment</th>\n",
              "      <th>published</th>\n",
              "      <th>duration</th>\n",
              "      <th>category</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>VID_18655</td>\n",
              "      <td>40</td>\n",
              "      <td>1031602</td>\n",
              "      <td>8523</td>\n",
              "      <td>363</td>\n",
              "      <td>1095</td>\n",
              "      <td>2016-09-14</td>\n",
              "      <td>PT7M37S</td>\n",
              "      <td>F</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>VID_14135</td>\n",
              "      <td>2</td>\n",
              "      <td>1707</td>\n",
              "      <td>56</td>\n",
              "      <td>2</td>\n",
              "      <td>6</td>\n",
              "      <td>2016-10-01</td>\n",
              "      <td>PT9M30S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>VID_2187</td>\n",
              "      <td>1</td>\n",
              "      <td>2023</td>\n",
              "      <td>25</td>\n",
              "      <td>0</td>\n",
              "      <td>2</td>\n",
              "      <td>2016-07-02</td>\n",
              "      <td>PT2M16S</td>\n",
              "      <td>C</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>VID_23096</td>\n",
              "      <td>6</td>\n",
              "      <td>620860</td>\n",
              "      <td>777</td>\n",
              "      <td>161</td>\n",
              "      <td>153</td>\n",
              "      <td>2016-07-27</td>\n",
              "      <td>PT4M22S</td>\n",
              "      <td>H</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>VID_10175</td>\n",
              "      <td>1</td>\n",
              "      <td>666</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>2016-06-29</td>\n",
              "      <td>PT31S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>14994</th>\n",
              "      <td>VID_31</td>\n",
              "      <td>2</td>\n",
              "      <td>525949</td>\n",
              "      <td>1137</td>\n",
              "      <td>83</td>\n",
              "      <td>86</td>\n",
              "      <td>2015-05-18</td>\n",
              "      <td>PT6M10S</td>\n",
              "      <td>A</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>14995</th>\n",
              "      <td>VID_5861</td>\n",
              "      <td>1</td>\n",
              "      <td>665673</td>\n",
              "      <td>3849</td>\n",
              "      <td>156</td>\n",
              "      <td>569</td>\n",
              "      <td>2015-10-20</td>\n",
              "      <td>PT3M56S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>14996</th>\n",
              "      <td>VID_805</td>\n",
              "      <td>4</td>\n",
              "      <td>3479</td>\n",
              "      <td>16</td>\n",
              "      <td>1</td>\n",
              "      <td>1</td>\n",
              "      <td>2013-08-23</td>\n",
              "      <td>PT3M13S</td>\n",
              "      <td>B</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>14997</th>\n",
              "      <td>VID_19843</td>\n",
              "      <td>1</td>\n",
              "      <td>963</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>0</td>\n",
              "      <td>2010-10-02</td>\n",
              "      <td>PT26S</td>\n",
              "      <td>G</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>14998</th>\n",
              "      <td>VID_8534</td>\n",
              "      <td>1</td>\n",
              "      <td>15212</td>\n",
              "      <td>22</td>\n",
              "      <td>5</td>\n",
              "      <td>4</td>\n",
              "      <td>2016-02-19</td>\n",
              "      <td>PT1M1S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>14999 rows × 9 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           vidid  adview    views likes  ... comment   published duration category\n",
              "0      VID_18655      40  1031602  8523  ...    1095  2016-09-14  PT7M37S        F\n",
              "1      VID_14135       2     1707    56  ...       6  2016-10-01  PT9M30S        D\n",
              "2       VID_2187       1     2023    25  ...       2  2016-07-02  PT2M16S        C\n",
              "3      VID_23096       6   620860   777  ...     153  2016-07-27  PT4M22S        H\n",
              "4      VID_10175       1      666     1  ...       0  2016-06-29    PT31S        D\n",
              "...          ...     ...      ...   ...  ...     ...         ...      ...      ...\n",
              "14994     VID_31       2   525949  1137  ...      86  2015-05-18  PT6M10S        A\n",
              "14995   VID_5861       1   665673  3849  ...     569  2015-10-20  PT3M56S        D\n",
              "14996    VID_805       4     3479    16  ...       1  2013-08-23  PT3M13S        B\n",
              "14997  VID_19843       1      963     0  ...       0  2010-10-02    PT26S        G\n",
              "14998   VID_8534       1    15212    22  ...       4  2016-02-19   PT1M1S        D\n",
              "\n",
              "[14999 rows x 9 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 75
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 402
        },
        "id": "B1951P8tiuxV",
        "outputId": "99bc14b0-1a8e-430e-df48-df5d56613959"
      },
      "source": [
        "test"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>vidid</th>\n",
              "      <th>views</th>\n",
              "      <th>likes</th>\n",
              "      <th>dislikes</th>\n",
              "      <th>comment</th>\n",
              "      <th>published</th>\n",
              "      <th>duration</th>\n",
              "      <th>category</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>VID_1054</td>\n",
              "      <td>440238</td>\n",
              "      <td>6153</td>\n",
              "      <td>218</td>\n",
              "      <td>1377</td>\n",
              "      <td>2017-02-18</td>\n",
              "      <td>PT7M29S</td>\n",
              "      <td>B</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>VID_18629</td>\n",
              "      <td>1040132</td>\n",
              "      <td>8171</td>\n",
              "      <td>340</td>\n",
              "      <td>1047</td>\n",
              "      <td>2016-06-28</td>\n",
              "      <td>PT6M29S</td>\n",
              "      <td>F</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>VID_13967</td>\n",
              "      <td>28534</td>\n",
              "      <td>31</td>\n",
              "      <td>11</td>\n",
              "      <td>1</td>\n",
              "      <td>2014-03-10</td>\n",
              "      <td>PT37M54S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>VID_19442</td>\n",
              "      <td>1316715</td>\n",
              "      <td>2284</td>\n",
              "      <td>250</td>\n",
              "      <td>274</td>\n",
              "      <td>2010-06-05</td>\n",
              "      <td>PT9M55S</td>\n",
              "      <td>G</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>VID_770</td>\n",
              "      <td>1893173</td>\n",
              "      <td>2519</td>\n",
              "      <td>225</td>\n",
              "      <td>116</td>\n",
              "      <td>2016-09-03</td>\n",
              "      <td>PT3M8S</td>\n",
              "      <td>B</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8759</th>\n",
              "      <td>VID_2944</td>\n",
              "      <td>111205</td>\n",
              "      <td>471</td>\n",
              "      <td>14</td>\n",
              "      <td>101</td>\n",
              "      <td>2011-04-19</td>\n",
              "      <td>PT3M14S</td>\n",
              "      <td>C</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8760</th>\n",
              "      <td>VID_8195</td>\n",
              "      <td>1074457</td>\n",
              "      <td>1024</td>\n",
              "      <td>195</td>\n",
              "      <td>65</td>\n",
              "      <td>2016-06-03</td>\n",
              "      <td>PT2H18M35S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8761</th>\n",
              "      <td>VID_18973</td>\n",
              "      <td>1891692</td>\n",
              "      <td>4606</td>\n",
              "      <td>1109</td>\n",
              "      <td>191</td>\n",
              "      <td>2016-06-26</td>\n",
              "      <td>PT7M14S</td>\n",
              "      <td>F</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8762</th>\n",
              "      <td>VID_5849</td>\n",
              "      <td>714859</td>\n",
              "      <td>1639</td>\n",
              "      <td>151</td>\n",
              "      <td>382</td>\n",
              "      <td>2016-03-23</td>\n",
              "      <td>PT3M15S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8763</th>\n",
              "      <td>VID_8494</td>\n",
              "      <td>5297</td>\n",
              "      <td>12</td>\n",
              "      <td>1</td>\n",
              "      <td>0</td>\n",
              "      <td>2016-12-19</td>\n",
              "      <td>PT10M14S</td>\n",
              "      <td>D</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>8764 rows × 8 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "          vidid    views likes  ...   published    duration category\n",
              "0      VID_1054   440238  6153  ...  2017-02-18     PT7M29S        B\n",
              "1     VID_18629  1040132  8171  ...  2016-06-28     PT6M29S        F\n",
              "2     VID_13967    28534    31  ...  2014-03-10    PT37M54S        D\n",
              "3     VID_19442  1316715  2284  ...  2010-06-05     PT9M55S        G\n",
              "4       VID_770  1893173  2519  ...  2016-09-03      PT3M8S        B\n",
              "...         ...      ...   ...  ...         ...         ...      ...\n",
              "8759   VID_2944   111205   471  ...  2011-04-19     PT3M14S        C\n",
              "8760   VID_8195  1074457  1024  ...  2016-06-03  PT2H18M35S        D\n",
              "8761  VID_18973  1891692  4606  ...  2016-06-26     PT7M14S        F\n",
              "8762   VID_5849   714859  1639  ...  2016-03-23     PT3M15S        D\n",
              "8763   VID_8494     5297    12  ...  2016-12-19    PT10M14S        D\n",
              "\n",
              "[8764 rows x 8 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 76
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "FYYelOhlXVaP"
      },
      "source": [
        "# Preprocessing"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "7r5N8K9xW8b0"
      },
      "source": [
        "categories = {\"A\": 1, \"B\": 2, \"C\": 3, \"D\": 4, \"E\": 5, \"F\": 6, \"G\": 7, \"H\": 8}\n",
        "\n",
        "data[\"category\"] = data[\"category\"].map(categories)\n",
        "test[\"category\"] = test[\"category\"].map(categories)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "qlomDS2bXAxj"
      },
      "source": [
        "for e in [\"views\", \"likes\", \"dislikes\", \"comment\"]:\n",
        "    data = data[data[e] != \"F\"]\n",
        "    for i in range(len(test)):\n",
        "        if test[e][i]=='F':\n",
        "            test.loc[i, e] = '0'\n",
        "\n",
        "for e in [\"views\", \"likes\", \"dislikes\", \"comment\", \"adview\"]:\n",
        "    data[e] = pd.to_numeric(data[e])\n",
        "    if e != 'adview':\n",
        "        test[e] = pd.to_numeric(test[e])\n",
        "\n",
        "data = data[data[\"adview\"] < 2000000]\n",
        "\n",
        "data[\"published\"] = LabelEncoder().fit_transform(data[\"published\"])\n",
        "test[\"published\"] = LabelEncoder().fit_transform(test[\"published\"])"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "ZyE6Z_8BXCsr"
      },
      "source": [
        "def checki(x):\n",
        "    y = x[2:]\n",
        "    h, m, s, mm = \"\", \"\", \"\", \"\"\n",
        "    P = [\"H\", \"M\", \"S\"]\n",
        "    for i in y:\n",
        "        if i not in P:\n",
        "            mm += i\n",
        "        else:\n",
        "            if i == P[0]:\n",
        "                h = mm\n",
        "                mm = \"\"\n",
        "            elif i == P[1]:\n",
        "                m = mm\n",
        "                mm = \"\"\n",
        "            else:\n",
        "                s = mm\n",
        "                mm = \"\"\n",
        "    if h == \"\":\n",
        "        h = \"00\"\n",
        "    if m == \"\":\n",
        "        m = \"00\"\n",
        "    if s == \"\":\n",
        "        s = \"00\"\n",
        "    bp = h + \":\" + m + \":\" + s\n",
        "    return bp"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "58NHAcg-XGbb"
      },
      "source": [
        "def func_sec(time_str):\n",
        "    h, m, s = time_str.split(\":\")\n",
        "    return int(h) * 3600 + int(m) * 60 + int(s)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "sMZ5HUqHW1kv"
      },
      "source": [
        "data[\"duration\"] = data[\"duration\"].apply(checki).apply(func_sec)\n",
        "test[\"duration\"] = test[\"duration\"].apply(checki).apply(func_sec)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "Xlx0g4YfXi_J"
      },
      "source": [
        "# Visualize correlation"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "CqUlHvYKXemW",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 487
        },
        "outputId": "1fde573f-20cf-4f88-fbd8-d7292bd840e0"
      },
      "source": [
        "f, ax = plt.subplots(figsize=(10, 8))\n",
        "corr = data.corr()\n",
        "sns.heatmap(corr, mask=np.zeros_like(corr, dtype=np.bool), cmap=sns.diverging_palette(220, 10, as_cmap=True),\n",
        "square=True, ax=ax,annot=True)\n",
        "plt.show()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x576 with 2 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "xuMe6gE1X3PX"
      },
      "source": [
        "# Prepare for input to model"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "kXjVHqlgXuh_"
      },
      "source": [
        "Y = pd.DataFrame(data.iloc[:, 1].values, columns=[\"target\"])\n",
        "test_y = pd.DataFrame(test.iloc[:, 0].values, columns=['vidid'])\n",
        "data = data.drop([\"adview\", \"vidid\"], axis=1)\n",
        "test = test.drop(['vidid'], axis=1)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "k4MYJjmpXzEv"
      },
      "source": [
        "X_train, X_val, y_train, y_val = train_test_split(\n",
        "    data, Y, test_size=0.1, random_state=42\n",
        ")\n",
        "y_train = y_train.to_numpy().ravel()\n",
        "y_val = y_val.to_numpy().ravel()"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "rzz_IrKiXqEP"
      },
      "source": [
        "sc_X = MinMaxScaler()\n",
        "X_train = sc_X.fit_transform(X_train)\n",
        "X_val = sc_X.transform(X_val)\n",
        "test = sc_X.transform(test)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "arJJOkgUYMVm"
      },
      "source": [
        "# Test on models"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "xMJdMnvuYCUj"
      },
      "source": [
        "def evaluate(X_val, y_val, model):\n",
        "    y_preds = model.predict(X_val)\n",
        "    print(\"RMSE : \", np.sqrt(mean_squared_error(y_val, y_preds)))\n",
        "    print(\"R^2 : \", r2_score(y_val, y_preds))\n",
        "    plt.figure(figsize=(20, 10))\n",
        "    plt.scatter(range(len(y_preds)), y_preds, color='blue', s=1)\n",
        "    plt.scatter(range(len(y_val)), y_val, color='red', s=1)\n",
        "    plt.show()"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "Hr8TtA0kGfbG"
      },
      "source": [
        "### Linear Regression"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 612
        },
        "id": "atFPHMSUGkpo",
        "outputId": "90dfa28b-ce6a-43ab-da74-b8be2b2556c2"
      },
      "source": [
        "linear_regression = LinearRegression()\n",
        "linear_regression.fit(X_train, y_train)\n",
        "print(\"Lineasr Regression\")\n",
        "evaluate(X_val, y_val, linear_regression)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Lineasr Regression\n",
            "RMSE :  10237.05145626168\n",
            "R^2 :  -0.022721187595377\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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GlwSEREREREREZGaaXBJRERERERERERqpsElERERERERERGpmQaXRERERERERESkZhpcEhERERERERGRmmlwSUREREREREREaqbBJRERERERERERqZkGl0REREREREREpGYaXBIRERERERERkZppcElERERERERERGqmwSUREREREREREamZBpdERERERERERKRmGlwSEREREREREZGaaXBJRERERERERERqpsElERERERERERGpmQaXRERERERERESkZhpcEhERERERERGRmmlwSUREREREREREaqbBJRERERERERERqZkGl0REREREREREpGbGWtvoMtSVMWYvsKPR5aiTdmBfowshTU9xInEpViQuxYrEoTiRuBQrEpdiReJSrDTGNGvtuVFPjLrBpdHEGLPJWjuv0eWQ5qY4kbgUKxKXYkXiUJxIXIoViUuxInEpVpqPvhYnIiIiIiIiIiI10+CSiIiIiIiIiIjUTINLze2uRhdARgTFicSlWJG4FCsSh+JE4lKsSFyKFYlLsdJkdM0lERERERERERGpmWYuiYiIiIiIiIhIzTS41ISMMYuNMd3GmKwx5vZGl0cayxgz1RjzuDHmBWPM88aYz3qPn2OMecQY87L3t8173Bhj/t2Ln98bY+Y2dg1kOBljTjPGbDXG/Ma7P8MYs96Lh58ZY1q8x0/37me956c3stwyvIwxE40xDxhjthljXjTGdCmnSBRjzOe8tuc5Y8xPjDFnKK8IgDHmXmNMjzHmucBjVecRY8wnvOVfNsZ8ohHrIkOnTJx83Wt/fm+M+YUxZmLguTu8OOk2xlwTeFzHR6NcVKwEnltmjLHGmHbvvnJKE9LgUpMxxpwG/AfwIeBi4AZjzMWNLZU0WB+wzFp7MXA58BkvJm4HHrXWzgQe9e6Di52Z3u3TwPeGv8jSQJ8FXgzc/xfgm9baFHAQuNl7/GbgoPf4N73l5NTxb8Bqa+0s4D24mFFOkRLGmA7gb4B51trZwGnA9SiviPN9YHHosaryiDHmHODLQCdwGfBlf0BKRo3v0z9OHgFmW2vfDbwE3AHg9W+vBy7xXvNd76SZjo9ODd+nf6xgjJkKXA3kAg8rpzQhDS41n8uArLX2FWttL/BTYEmDyyQNZK19w1q7xfv/KO4gsAMXFz/wFvsB8DHv/yXAD63zNDDRGHPBMBdbGsAYMwX4E+Bu774BrgQe8BYJx4kfPw8Ai7zlZZQzxkwAFgD3AFhre621h1BOkWhjgTONMWOBBPAGyisCWGvXAAdCD1ebR64BHrHWHrDWHsQNOvQ7uJSRKypOrLW/tdb2eXefBqZ4/y8BfmqtPWGt3Q5kccdGOj46BZTJKeBOVtwGBC8WrZzShDS41Hw6gNcC93d6j4ngfcVgDrAemGytfcN7ajcw2ftfMXTq+hau8f0/7/4k4FCgAxeMhUKceM8f9paX0W8GsBe4z/sK5d3GmFaUUyTEWrsL+Ffc2eI3cHliM8orUl61eUT5RT4JPOT9rziREsaYJcAua+2zoacUK01Ig0siI4Qx5izg58DfWmuPBJ+z7mcf9dOPpzBjzIeBHmvt5kaXRZreWGAu8D1r7RzgOMWvrgDKKeJ4XyVYghuQ/COgFZ0BlpiUR2Qgxpgv4i7/8ONGl0WajzEmAXwB+IdGl0Xi0eBS89kFTA3cn+I9JqcwY8w43MDSj621q7yH9/hfTfH+9niPK4ZOTfOBjxpjXsVNF78Sd12did7XWaA0Fgpx4j0/Adg/nAWWhtkJ7LTWrvfuP4AbbFJOkbCrgO3W2r3W2pPAKlyuUV6RcqrNI8ovpyhjzF8AHwY+7g1EguJESl2IO7nxrNe/nQJsMcacj2KlKWlwqflsBGZ6v8TSgruo3a8aXCZpIO96FfcAL1prvxF46leA/wsInwD+O/D4n3u/onA5cDgwRV1GKWvtHdbaKdba6bi88Zi19uPA48B13mLhOPHj5zpveZ1hPgVYa3cDrxljLvIeWgS8gHKK9JcDLjfGJLy2yI8V5RUpp9o88jBwtTGmzZspd7X3mIxixpjFuK/xf9Ramw889SvgeuN+eXIG7mLNG9Dx0SnJWpux1p5nrZ3u9W93AnO9foxyShMaO/AiMpystX3GmL/G7QSnAfdaa59vcLGkseYDNwIZY8wz3mNfAL4G3G+MuRnYAfyZ99yDwLW4iyDmgZuGt7jSZP4e+Kkx5qvAVryLOHt/f2SMyeIunnh9g8onjXEr8GOvk/4KLk+MQTlFAqy1640xDwBbcF9d2QrcBfwPyiunPGPMT4APAu3GmJ24X2iqqm9irT1gjFmBGzwA+Iq1NuqCvjJClYmTO4DTgUe8a/4/ba39S2vt88aY+3GD2H3AZ6y1b3vvo+OjUS4qVqy195RZXDmlCRmdUBIRERERERERkVrpa3EiIiIiIiIiIlIzDS6JiIiIiIiIiEjNNLgkIiIiIiIiIiI10+CSiIiIiIiIiIjUTINLIiIiIiIiIiJSMw0uiYiIiIiIiIhIzTS4JCIiIiIiIiIiNdPgkoiIiIiIiIiI1Oz/AbkEJz6/5pMVAAAAAElFTkSuQmCC\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "JqdWm-0SZVFO"
      },
      "source": [
        "### SVR"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Y8hM0aVDYErt",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 612
        },
        "outputId": "817c9130-ff88-4a67-dae1-5ee9986941ed"
      },
      "source": [
        "svr_model = SVR()\n",
        "svr_model.fit(X_train, y_train)\n",
        "print(\"SVR\")\n",
        "evaluate(X_val, y_val, svr_model)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "SVR\n",
            "RMSE :  10176.569923886505\n",
            "R^2 :  -0.010672206520453864\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "rJiyu6xSZZw8"
      },
      "source": [
        "### Decision Tree"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "UXFAl34LYGGP",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 612
        },
        "outputId": "a7f16aa6-e4f4-4cbc-fe8b-f6ff1167c526"
      },
      "source": [
        "dt_model = DecisionTreeRegressor()\n",
        "dt_model.fit(X_train, y_train)\n",
        "print(\"Decision Tree\")\n",
        "evaluate(X_val, y_val, dt_model)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Decision Tree\n",
            "RMSE :  16061.469213188506\n",
            "R^2 :  -1.5175495088039592\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "ToGxpjhMZdJ0"
      },
      "source": [
        "### Random Forest"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "ehitUt3hYIgG",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 612
        },
        "outputId": "da93118b-06c0-42ae-8ab3-38bc01951fcc"
      },
      "source": [
        "rf_model = RandomForestRegressor()\n",
        "rf_model.fit(X_train, y_train)\n",
        "print(\"Random Forest\")\n",
        "evaluate(X_val, y_val, rf_model)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Random Forest\n",
            "RMSE :  14867.797228361878\n",
            "R^2 :  -1.1572513010585999\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "YKMXVyrmfphf"
      },
      "source": [
        "### Random Forest w/ Hyperparameter tuning"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "QM7nqGK5f5ke"
      },
      "source": [
        "n_estimators = [int(x) for x in np.linspace(50, 300, num = 10)]\n",
        "max_features = ['auto', 'sqrt']\n",
        "max_depth = [int(x) for x in np.linspace(1, 60, num = 3)]\n",
        "min_samples_split = [5, 10]\n",
        "\n",
        "random_grid = {'n_estimators': n_estimators,\n",
        "               'max_features': max_features,\n",
        "               'max_depth': max_depth,\n",
        "               'min_samples_split': min_samples_split}"
      ],
      "execution_count": 105,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 662
        },
        "id": "ZbLcKpUagwdR",
        "outputId": "6e9de69a-ea41-4c72-b79d-6d81cf838545"
      },
      "source": [
        "rf_random = RandomizedSearchCV(estimator=rf_model, param_distributions = random_grid, n_iter = 10, cv = 10, n_jobs=-1, verbose=1, scoring='neg_mean_squared_error')\n",
        "rf_random.fit(X_train, y_train)\n",
        "evaluate(X_val, y_val, rf_random.best_estimator_)"
      ],
      "execution_count": 113,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Fitting 10 folds for each of 10 candidates, totalling 100 fits\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "stream",
          "text": [
            "[Parallel(n_jobs=-1)]: Using backend LokyBackend with 2 concurrent workers.\n",
            "[Parallel(n_jobs=-1)]: Done  46 tasks      | elapsed:  1.2min\n",
            "[Parallel(n_jobs=-1)]: Done 100 out of 100 | elapsed:  4.3min finished\n"
          ],
          "name": "stderr"
        },
        {
          "output_type": "stream",
          "text": [
            "RMSE :  11323.579930504307\n",
            "R^2 :  -0.2513390015092638\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "jjuOoCbtXcZF"
      },
      "source": [
        "### Neural Network"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "xc1Ex6QlTl7s"
      },
      "source": [
        "nn_model = keras.models.Sequential([\n",
        "                keras.layers.Dense(16, input_shape=(7,), activation='relu'),\n",
        "                keras.layers.Dropout(0.2),\n",
        "                keras.layers.Dense(16, activation='relu'),\n",
        "                keras.layers.Dropout(0.2),\n",
        "                keras.layers.Dense(8, activation='relu'),\n",
        "                keras.layers.Dense(units=1)\n",
        "])"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "X_WutTBNU8xk"
      },
      "source": [
        "nn_model.compile(optimizer='rmsprop', loss='mse', metrics=['mse'])"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 595
        },
        "id": "cDEvOYRvVWpK",
        "outputId": "9f14dc8b-8ac9-49cd-9424-9fa3335081e1"
      },
      "source": [
        "nn_model.fit(X_train, y_train, epochs=200, verbose=0)\n",
        "evaluate(X_val, y_val, nn_model)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "RMSE :  10071.840114747678\n",
            "R^2 :  0.010022950205684777\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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c5x+mw332ZdybC+0zF27uuvSb27p2sKlBjjz7cPF6sTw+WS4DIuVfe52mO8/yFaKxcKlp1x55ZWH72tTe1FKeUqSTyQ5P8mvJnn3VVdd9akrr7xy10LTvO9977vy0ksvPVRFPGjWrk1WrUq+/e3kiiuSc8/t/r797eSuu5Jrr002bkxWr04uu6x73bAhGRnppp+eTrZt624jvfrq5Lbbkuc/f+bzbdu6uyceeCBZv767i2J0NPn855Mzz+zG6cc/7bZtecntV+RvX/DDOf415z05j76MY2PJunXd+9e/PjnuuOTRR5N3vSt5xaPbctp1m/PiH1+Ve0c25Ad+oFuP3buTO+5Ijj++W/batd3yN2xIbrtrJL96+4ZsOG8k5547e33Wru3K1Jd/ZKRbzm/8RjfPbdu69elvn52e7paTJLfc0o0/PM+7Lt6Wk6/Z3BV+w4Z590U/j6c+Nbnppm6bvf713TbvX/v99MADyfnnz6zLyEj3deOLL+7K9eEPz/wwwa/9Wjes3x9r1ya/fdfa/P7tq3L7c8Zyyj8fyS23JKOZzhMT2/KR+9dm3YaRnHdet9127OjW5fOfT846q5v3FVckt9/eLfeuu7pynXlmt20uuqjbTkm3PsN1ot8fd901M2x4m+/e3X0j+F3v6rbBpk3dun/4w139O/PM5Kd/OrnmmplNOVw/du+eeRZivw1WrUruvXdmP37uczP7d2RkZvuNjiY7dyaveU3yxS92yzrvvG5/XHFFN5/zzuvG372729bXXNPti0cemRlnuEwXXthtt3PPTd70puTpT+/W//u/P7n++m6Zk5NdvT7vvO79Ndcka9Ykl1ySvO513X7fsSM55piZ7dbfRVVrcsop3fS//dvJOeckf/3XyU/+ZHds3HhjV76vPTSdJ7Zuy8d3rs0DO0dy/vndNl+9uqs369bNHLPD9fHii7s24JnP7F777Twyknz9691+uvbamfreHy833JD82Z912/MnfmL+tqcf9557kvvvnzlOP//5mWNg9epueZOT3bbs/90fgxde2LUDSbd9jzuuK9fNN3fzveyy2be4D++Xvj3o57V27cx2+Pa3k1e9qptf3xZecUV3bF9//Ux7dvXV3Ta++uquzp97bvLDP7zw/CcnZ46d4XrZH8/D+6BvD849tytL32aefHL3Otw+7dzZba/jMp2ND27Lr318bT5++8ge9X2+Nq7fzzfe2LX13/d9M+s7MtIdv7fd1k373Od2x1XfHvV1YdjIyMy63nFHN58dO2bahHvvndmXw+3atdd2x/s739kN67dhf4wM7/++/u/c2R0vq1d3jzPohw+v9+jqkZz8ExsyMjoy63y140+n8/NP3ZZTfmRtzt00kl/91dl1ZffurqyrV88ux7p1yboNI7n5iQ35N68byejo7Lb7rru69bjqqpnzVV9nJya6bfmqV82uy8PPb/2n69fmT3esyt3/bCyjq0eerEdzt/PcY7Vfr+HtPny6GT4/jI4m33nu2rz/hlW58m/Gcsc9I9m5syvXzp1dXein7evYqlVdOYaX07+uXZvc/9Pbcu6Nm3P9Lauy+tUbnlz3Cy/s2uxrr+3qfd9m/cIvdNPv3t3V/5ddNJKPf31Dvv3Ukdx1V1cXv/u79zyWR0e7+vTSl3bbeffublvfc8/M+enCC7vX+Y6/uevymtd0beVb39od24tt67Vrk9Uj0znjjm15w290bWl/Lrrwwq59uPPOmfn3x+9ll80+x/TntGuu6drsv/iL5Pd/v9tGfRsyfGz1x9TOnd37X//17t/Dx0jfFt1xR3eO6M9R117bTXfqqV3ZhvsVw8vo693f/9hY3nL8ZF7x6c057dxVef5lG7JqVbJly8w069Z187nppm6fztd+zW3fb755z7LdeGN3rluzJnnLW2ba2+c/v6szN92UfPrTM/3Svm2/+eZuHued19XZuX274f08Nja7z9ifK/p6Prw9htvtuXVg+Di4995u323c2NWfN72pq0P/6T8lJ544sw5z2/PhdjdJnpjYlqddsTl/umNV3vAba7P2lm35d+9cm798dCQ33jh/XeiP4z/7s+R3f7crwy/8wuxy33Zbt1927kz+6I+67TTcts3tc/Xvh/uTc8e55+bp/L9f3ZY/+ou1efGmkbz+9XvWuZGR7jbkj52/Lf/hI905qG9H+r5Wv4yzzurK97M/2/VdfuzHuv7O3O2+dm3y8MjanHbuqvzOU8fyhitGcs89yUc+0tWPfh/0x9gXLtuWV965OV94dFVO/omu8Xv+87v91o8zd/7D/Zp+ve+4Y+b65/u+b6beTkx063zPPV15N22ama5v3888s6tPl1zS1f2LLurq3vD54YQTZrfR/XJvvrlrz771re6Y/cmfnN0uDbf1l1zSnVfvu69rezZu7Ma/7LLZ1ydzr6H6Ps5dd3XL6vsjfTkmJrq/vm/3wANdfe/Pa8N1Zm6/ou+T9X2opz+967v+yI906/9DP5R86UvdMd9fI/2TfzJT5mSm3br99u7fExPddly3bva5vx+nP99//etde3Heeckv//JMm7tjR1eeq6/u6t18jz3atm1236yvs2eeO5JvPH9Dbrtr5Ml+6apV3TwuumimXMN1Y25/sD+3D1879+X6sR+bucZYvTp52tNmrvEWapf6Pts11ySf+ETyh3/Yzee882bayOFj8kh31VVXPXrllVe+b77PShc+LU+llLOTXFlrPX/w77ckSa31VxeaZv369XX79u2HqIQHz/R0dyDs3t39uw8Atm7tOj0vfvHMvy+/PPmrv+o6YR/9aPLZz3YH7J13Js95TvdZ0jUawx2Nz342+dSnZsbpXzdunGkQ165NHr1vOv+mTObi28eybuNo1q7tGs3TT+8OxAsuSN72tu5k9J73JKed1pUrSV69cTrXbpjMZMYyndFcdVXXMO/Y0X2+alWya1c3bHIy+e//vTtgr7++W7/du7tlfec7XeM0Pj5zEN93X1LKTEN33HHJY49163HSSV357r+/+/ycc7p1Pfvs5BnP6Mr7trcln71xOtf94GQ+UMeyZk1yzl9O5lPPHcvn/2Y0b3xj8o53dNvy9tu7Ttwdd3Tbpt8H//qC6ex862Q+vXYsdzw4+uR227Ch2y5vfWvX2O7cObP977svefjhbthznpM85SnJ1FQ3z6RrZDdvTv5uajp/8prJ/KP/9UT+7cNXZTwTedbEeMbGZjqA/bYcXueka2inprr355yT/I//MfPvfv/276++OnnDG7r59cPWrUsevGM63//pydy/fixTfzc6a/p167qT+C23dCe2H/iB7uS2cWNy3rrp/GQm81uDfb5790x5+jrV15+ke/+M/zmd0z7XLWv3yGhOP32mnvbL6de1v+19YqKrw8Pjzq3TZ5/dnSD6ZT34YPLGNyZX/ex0/sXUZFaNj6WsGs0f/3G3jL4O9fXlR8+ezuueMZn/+21j+bm3jebGG7s6eM89M/Wid8453XKGt/34eHcyeuKJbty+no+NDS7s12zNpVOb897nTOSBTeN58MHZ69pv62H9Ovb7rj9WhrfVcJle/OKuLvbLnNsO9MfYmjVdudes6ebfr0Pf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yik1SJNLtacTOBh5fihYJkJwi8EyYBvQ4Zw7HKw6AnQEvyuGLl9/j9/5/ip4PhM4ERnARWNhPE6C9QPB9nLpWwAcA/4luIXyfjNrQjlF8jjneoG/w3+3+DB+nngO5RUpbbJ5RPlFfh/4cfC74kRymNkdQK9zblfeKsVKDdLkkshFwsyuBL4P/LFz7mR0nfP/7aP+9eNlzMzeD/Q5556rdlmk5tUBy4GvO+eWAUNkb10BlFPEF9xKcAf+hOSbgCb0DrCUSXlEzsbM/gL/6x++Xe2ySO0xsxjwOeAvq10WKY8ml2pPLzAv8nxusEwuY2Y2FX9i6dvOuU3B4qPhrSnBz75guWLo8nQT8AEz24//cfFb8b9XpzW4nQVyY2E8ToL1LcDrF7LAUjWHgEPOuW3B84fxJ5uUUyTfbcA+59wx59wosAk/1yivSCmTzSPKL5cpM/td4P3Ah4OJSFCcSK634L+5sSsY384FdprZHBQrNUmTS7XnWeCa4D+xTMP/UrtHqlwmqaLg+yoeAF5xzn05suoRIPwPCB8D/jOy/KPBf1G4ARiIfERdLlHOuXucc3Odc/Px88Zm59yHgaeADwab5cdJGD8fDLbXO8yXAefcEeCgmS0MFr0beBnlFCmUBm4ws1jQF4WxorwipUw2jzwOvMfM2oJPyr0nWCaXMDNbi38b/wecc5nIqkeAO83/z5ML8L+seTu6ProsOeeSzrnZzrn5wfj2ELA8GMcop9SgurNvIheSc27MzD6F3wimAA8653ZXuVhSXTcBHwGSZvZCsOxzwF8D3zOzjwMHgN8J1j0KvA//SxAzwO9d2OJKjflz4Dtm9iXgeYIvcQ5+/puZpfC/PPHOKpVPquNu4NvBIP1V/DxxBcopEuGc22ZmDwM78W9deR64D/gRyiuXPTN7CHgXMMvMDuH/h6ZJjU2cc8fNrBt/8gDgi865Yl/oKxepEnFyD1APPBF85/8zzrk/cs7tNrPv4U9ijwGfdM69EbyOro8uccVixTn3QInNlVNqkOkNJRERERERERERqZRuixMRERERERERkYppcklERERERERERCqmySUREREREREREamYJpdERERERERERKRimlwSEREREREREZGKaXJJREREREREREQqpsklERERERERERGpmCaXRERERERERESkYv8Pyk+W4DuWUz0AAAAASUVORK5CYII=\n",
            "text/plain": [
              "<Figure size 1440x720 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "begwdeaKZvtt"
      },
      "source": [
        "# Save model"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "L1NpWE5FZvBZ",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "e443327a-0e30-4e2b-939c-600763f8e8da"
      },
      "source": [
        "joblib.dump(svr_model, \"dt_model.pkl\")"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "['dt_model.pkl']"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 94
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "1L90-EzkXsgr"
      },
      "source": [
        "nn_model.save('nn_model.h5')"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "Su78gMA_cHxI"
      },
      "source": [
        "# Load model"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "RLkGzuikcKmK"
      },
      "source": [
        "model = joblib.load('dt_model.pkl')"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "hIQg1B_TXjUl"
      },
      "source": [
        "model = keras.models.load_model('nn_model.h5')"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "rbLRl30IcQod"
      },
      "source": [
        "preds = model.predict(test)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "-otRnqzPcZBz"
      },
      "source": [
        "preds = preds.tolist()\n",
        "d = {'adview': preds}\n",
        "pd.DataFrame(d).to_csv('predictions.csv', header=False, index=False)"
      ],
      "execution_count": null,
      "outputs": []
    }
  ]
}
