import streamlit as st
import pandas as pd
import numpy as np
import pydeck as pdk
import datetime

st.title('Uber pickups in NYC !!')

DATE_COLUMN = 'date/time'
DATA_URL = ('https://s3-us-west-2.amazonaws.com/'
         'streamlit-demo-data/uber-raw-data-sep14.csv.gz')

@st.cache_data

# Fetching Data
def load_data(nrows):
    data = pd.read_csv(DATA_URL, nrows=nrows)
    lowercase = lambda x: str(x).lower()
    data.rename(lowercase, axis='columns', inplace=True)
    data[DATE_COLUMN] = pd.to_datetime(data[DATE_COLUMN])
    return data

# Create a text element and let the reader know the data is loading.
data_load_state = st.text('Loading data...')
# Load 10,000 rows of data into the dataframe.
data = load_data(10000)
# Notify the reader that the data was successfully loaded.
data_load_state.text("Loading Completed!")

# data

# Create Sub-Header
if st.checkbox('Show raw data'):
    st.subheader('Raw data')
    st.write(data)

# Draw a histogram
st.subheader('Number of pickups by hour')
hist_values = np.histogram(
    data[DATE_COLUMN].dt.hour, bins=24, range=(0,24))[0]
st.bar_chart(hist_values)

# Adding Slider
hour_to_filter = st.slider('hour', 0, 23, 17)  # min: 0h, max: 23h, default: 17h
date_filter = st.date_input("Select Date", datetime.date(2014, 9, 1))
st.write("Selected Date:", date_filter)

filtered_data = data[(data[DATE_COLUMN].dt.hour == hour_to_filter)
                    & (data[DATE_COLUMN].dt.date == date_filter)
                      ]
st.subheader(f'2D Map of all pickups at {hour_to_filter}:00 on {date_filter}')
st.map(filtered_data)

# Create 3D Maps
st.subheader(f'3D Map of all pickups at {hour_to_filter}:00 on {date_filter}')
chart_data = pd.DataFrame(
    np.random.randn(1000, 2) / [50, 50] + [37.76, -122.4],
    columns=["lat", "lon"],
)

st.pydeck_chart(
    pdk.Deck(
        map_style=None,
        initial_view_state=pdk.ViewState(
            latitude=40.7128,
            longitude=-74.0060,
            zoom=11,
            pitch=50,
        ),
        layers=[
            pdk.Layer(
                "HexagonLayer",
                data=filtered_data,
                get_position="[lon, lat]",
                radius=200,
                elevation_scale=4,
                elevation_range=[0, 1000],
                pickable=True,
                extruded=True,
            ),
            pdk.Layer(
                "ScatterplotLayer",
                data=filtered_data,
                get_position="[lon, lat]",
                get_color="[200, 30, 0, 160]",
                get_radius=200,
            ),
        ],
    )
)

# Selection Box
option = st.selectbox(
    "Which city are you located in?",
    ("Bronx", "Brooklyn", "Manhattan", "Queens", "Staten Island"),
    index=None,
    placeholder="Select state",
)

st.write("You selected:", option)

# Create Plotly Graph
st.subheader(f'Area Chart of all pickups (Plotly)')
import plotly.express as px

data['hour'] = data['date/time'].dt.hour
hourly_counts = data.groupby('hour').size().reset_index(name='count')
print(hourly_counts)

# st.write(hourly_counts)

fig = px.area(hourly_counts, x="hour", y="count" )
st.plotly_chart(fig)

# Counter
if "counter" not in st.session_state:
    st.session_state.counter = 0

st.session_state.counter += 1

st.header(f"This page has run {st.session_state.counter} times.")
st.button("Run it again")
