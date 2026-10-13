# RL Stock Price Prediction

Dự án áp dụng **Deep Reinforcement Learning (DRL)** cho bài toán giao dịch cổ
phiếu. Mã nguồn được xây dựng trên nền tảng [FinRL](https://github.com/AI4Finance-Foundation/FinRL)
và cung cấp một pipeline hoàn chỉnh:

1. Tải dữ liệu thị trường từ Yahoo Finance.
2. Tiền xử lý dữ liệu và tạo các chỉ báo kỹ thuật.
3. Xây dựng môi trường giao dịch tương thích với Gymnasium.
4. Huấn luyện các tác tử A2C, DDPG, PPO, TD3 và SAC bằng Stable-Baselines3.
5. Backtest và so sánh với các đường cơ sở MVO và chỉ số DJIA.

> **Lưu ý:** Đây là dự án nghiên cứu/giáo dục. Kết quả backtest không bảo đảm
> lợi nhuận trong tương lai và không phải là lời khuyên đầu tư.

## Tính năng chính

- Thu thập dữ liệu cổ phiếu bằng `yfinance` hoặc `YahooDownloader`.
- Hỗ trợ bộ dữ liệu nhiều mã cổ phiếu, mặc định là nhóm DOW 30.
- Tạo các đặc trưng như MACD, RSI, VIX và turbulence index.
- Môi trường giao dịch có thể cấu hình vốn ban đầu, phí mua/bán, số lượng cổ
  phiếu và reward scaling.
- Tích hợp Stable-Baselines3, ElegantRL và RLlib thông qua lớp agent của FinRL.
- Lưu model, log TensorBoard và kết quả backtest để phân tích.
- Có notebook, ví dụ thực hành và unit test đi kèm.

## Cấu trúc dự án

```text
.
├── examples/                 # Pipeline mẫu tải dữ liệu, train và backtest
├── finrl/
│   ├── agents/               # Adapter cho các thư viện DRL
│   ├── applications/         # Các bài toán giao dịch mẫu
│   ├── meta/
│   │   ├── preprocessor/     # Downloader và tiền xử lý dữ liệu
│   │   └── env_stock_trading/# Môi trường giao dịch cổ phiếu
│   ├── config.py             # Tham số, thư mục output và ngày dữ liệu
│   ├── main.py
│   ├── train.py
│   ├── test.py
│   ├── trade.py
│   └── plot.py
├── unit_tests/               # Unit test
├── docs/                     # Tài liệu Sphinx
├── requirements.txt
└── pyproject.toml
```

## Yêu cầu

- Python 3.7 trở lên; nên dùng Python 3.10 hoặc 3.11 để tương thích tốt hơn
  với các thư viện hiện tại.
- Git.
- Kết nối Internet khi tải dữ liệu Yahoo Finance.
- Một số tính năng tùy chọn có thể cần TA-Lib, CUDA hoặc API key của nhà cung
  cấp dữ liệu tương ứng.

## Cài đặt

### Windows PowerShell

```powershell
git clone https://github.com/PVanDuc2k5/RL--Stock-Price-Prediction.git
cd "RL--Stock-Price-Prediction"
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install -e .
```

Nếu PowerShell chặn việc kích hoạt môi trường, có thể chạy trực tiếp Python
trong môi trường `.venv` hoặc điều chỉnh execution policy theo chính sách máy
của bạn.

### Linux/macOS

```bash
git clone https://github.com/PVanDuc2k5/RL--Stock-Price-Prediction.git
cd RL--Stock-Price-Prediction
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install -e .
```

## Quy trình chạy mẫu

Các script dưới đây thực hiện pipeline ba bước. Nên chạy từ thư mục gốc của
repository.

### 1. Tải và tiền xử lý dữ liệu

```bash
python examples/FinRL_StockTrading_2026_1_data.py
```

Script tải dữ liệu DOW 30 từ Yahoo Finance, thêm chỉ báo kỹ thuật, VIX và
turbulence index, sau đó tạo:

- `train_data.csv`: dữ liệu huấn luyện.
- `trade_data.csv`: dữ liệu dùng cho backtest.

Các khoảng thời gian và danh sách mã cổ phiếu có thể điều chỉnh trong
`finrl/config.py` và `finrl/config_tickers.py`.

### 2. Huấn luyện tác tử DRL

```bash
python examples/FinRL_StockTrading_2026_2_train.py
```

Script mẫu huấn luyện A2C, DDPG, PPO, TD3 và SAC. Model được lưu trong
`trained_models/`, còn log huấn luyện được ghi vào `results/`.

Để thử nhanh, giảm `total_timesteps` trong script. Khi đánh giá nghiêm túc,
nên tăng số bước, kiểm tra nhiều khoảng thời gian và sử dụng nhiều seed.

### 3. Backtest và so sánh

```bash
python examples/FinRL_StockTrading_2026_3_Backtest.py
```

Backtest tải các model đã huấn luyện, so sánh với MVO và DJIA, sau đó xuất
biểu đồ `backtest_result.png`. Cần chạy bước huấn luyện trước hoặc cung cấp
đúng các file model mà script backtest yêu cầu.

## Theo dõi bằng TensorBoard

Sau khi huấn luyện, có thể mở log bằng:

```bash
tensorboard --logdir results
```

Sau đó truy cập địa chỉ được TensorBoard in ra, thường là
`http://localhost:6006`.

## Chạy unit test

```bash
pytest unit_tests
```

## Tài liệu và ví dụ khác

- Tài liệu chi tiết nằm trong thư mục [`docs/`](docs/).
- Các ví dụ và notebook nằm trong [`examples/`](examples/).
- Mô tả kiến trúc các lớp FinRL nằm trong [`finrl/README.md`](finrl/README.md).
- Hướng dẫn pipeline mẫu nằm trong [`examples/README.md`](examples/README.md).

## Đóng góp

1. Fork repository và tạo một branch mới.
2. Thực hiện thay đổi, bổ sung test nếu cần.
3. Chạy formatter/linter và unit test liên quan.
4. Tạo pull request kèm mô tả dữ liệu, thời gian backtest và cách tái hiện
   kết quả.

## Giấy phép

Dự án được phát hành theo giấy phép [MIT](LICENSE).
