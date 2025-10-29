import pandas as pd
import mplfinance as mpf
import tkinter as tk
from tkinter import filedialog, messagebox

def load_data_from_file():
    """Hiển thị file dialog và load CSV hoặc Excel"""
    filepath = filedialog.askopenfilename(
        title="Chọn file CSV hoặc Excel",
        filetypes=[("CSV files","*.csv"),("Excel files","*.xlsx")]
    )
    if not filepath:
        messagebox.showinfo("Thông báo", "Không chọn file nào. Thoát chương trình.")
        return None

    if filepath.endswith(".csv"):
        df = pd.read_csv(filepath)
    elif filepath.endswith(".xlsx"):
        df = pd.read_excel(filepath)
    else:
        messagebox.showerror("Lỗi", "File không hợp lệ. Chỉ CSV hoặc Excel.")
        return None
    return df

def prepare_dataframe(df, timeframe='1min'):
    """Chuẩn hóa DataFrame và tạo OHLCV từ Tick data"""
    required_cols = ['Timestamp','Bid']
    for col in required_cols:
        if col not in df.columns:
            messagebox.showerror("Lỗi", f"File không có cột '{col}'.")
            return None

    # Chuyển Timestamp sang datetime
    df['Timestamp'] = pd.to_datetime(df['Timestamp'])
    df.set_index('Timestamp', inplace=True)

    # Nếu muốn, có thể dùng giá trung bình (Bid+Ask)/2
    if 'Ask' in df.columns:
        df['Price'] = (df['Bid'] + df['Ask']) / 2
    else:
        df['Price'] = df['Bid']

    # Resample theo timeframe để tạo OHLCV
    ohlc_dict = {
        'Price':'ohlc',
        'Bid':'count'  # dùng count làm volume tick
    }
    df_ohlc = df.resample(timeframe).agg(ohlc_dict)
    df_ohlc.columns = ['open','high','low','close','volume']

    # Drop các nến rỗng
    df_ohlc.dropna(inplace=True)
    return df_ohlc

def visualize_data(df, title="Chart"):
    """Vẽ biểu đồ nến + volume"""
    mpf.plot(df, type='candle', volume=True, style='charles', title=title)

def main():
    root = tk.Tk()
    root.withdraw()  # Ẩn cửa sổ chính

    df = load_data_from_file()
    if df is None:
        return

    df_ohlc = prepare_dataframe(df, timeframe='1min')
    if df_ohlc is None:
        return

    visualize_data(df_ohlc, title="Trading Chart")

if __name__ == "__main__":
    main()

