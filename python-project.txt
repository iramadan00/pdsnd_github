cities = ['chicago', 'new york city', 'washington']
    while True:
        city = input("Please enter a city (chicago, new york city, washington): ").strip().lower()
        if city in cities:
            break
        else:
            print("Invalid input. Please choose from chicago, new york city, or washington.")

    # Get user input for month
    months = ['january', 'february', 'march', 'april', 'may', 'june', 'all']
    while True:
        month = input("Please enter a month (january to june) or 'all': ").strip().lower()
        if month in months:
            break
        else:
            print("Invalid input. Please choose from january, february, march, april, may, june, or 'all'.")

    # Get user input for day of week
    days = ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday', 'all']
    while True:
        day = input("Please enter a day of the week (e.g., monday) or 'all': ").strip().lower()
        if day in days:
            break
        else:
            print("Invalid input. Please choose a valid day of the week or 'all'.")

    print('-' * 40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """

    # Load data file into a DataFrame
    df = pd.read_csv(CITY_DATA[city])

    # Convert the Start Time column to datetime
    df['Start Time'] = pd.to_datetime(df['Start Time'])

    # Extract month and day of week from Start Time
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.day_name()

    # Filter by month if applicable
    if month != 'all':
        # Use the index of the months list to get the corresponding int
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month_index = months.index(month) + 1
        df = df[df['month'] == month_index]

    # Filter by day of week if applicable
    if day != 'all':
        df = df[df['day_of_week'].str.lower() == day.lower()]

    return df
def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # Extract month, day of week, and hour from 'Start Time'
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.day_name()
    df['hour'] = df['Start Time'].dt.hour

    # Display the most common month
    common_month = df['month'].mode()[0]
    print("Most Common Month:", common_month)

    # Display the most common day of week
    common_day = df['day_of_week'].mode()[0]
    print("Most Common Day of Week:", common_day)

    # Display the most common start hour
    common_hour = df['hour'].mode()[0]
    print("Most Common Start Hour:", common_hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # Display most commonly used start station
    common_start = df['Start Station'].mode()[0]
    print("Most Commonly Used Start Station:", common_start)

    # Display most commonly used end station
    common_end = df['End Station'].mode()[0]
    print("Most Commonly Used End Station:", common_end)

    # Display most frequent combination of start and end station trip
    df['Trip Combination'] = df['Start Station'] + " to " + df['End Station']
    common_trip = df['Trip Combination'].mode()[0]
    print("Most Frequent Trip Combination:", common_trip)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # Display total travel time
    total_duration = df['Trip Duration'].sum()
    print("Total Travel Time: {:.2f} seconds ({:.2f} hours)".format(total_duration, total_duration / 3600))

    # Display mean travel time
    mean_duration = df['Trip Duration'].mean()
    print("Mean Travel Time: {:.2f} seconds ({:.2f} minutes)".format(mean_duration, mean_duration / 60))

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # Display counts of user types
    print("Counts of User Types:")
    print(df['User Type'].value_counts())
    print()

    # Display counts of gender (only if available)
    if 'Gender' in df.columns:
        print("Counts of Gender:")
        print(df['Gender'].value_counts())
        print()
    else:
        print("Gender data not available for this city.\n")

    # Display earliest, most recent, and most common year of birth (only if available)
    if 'Birth Year' in df.columns:
        earliest = int(df['Birth Year'].min())
        recent = int(df['Birth Year'].max())
        common = int(df['Birth Year'].mode()[0])
        print(f"Earliest Year of Birth: {earliest}")
        print(f"Most Recent Year of Birth: {recent}")
        print(f"Most Common Year of Birth: {common}")
    else:
        print("Birth year data not available for this city.")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)
