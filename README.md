# shuaxinjiazai
刷新加载


    import React, { Component } from "react";
    import { Platform, StyleSheet, Text, View, Image } from "react-native";
    import RefreshListView, { RefreshState } from "react-native-refresh-list-view";

    export default class Tab2 extends Component {
      constructor(props) {
        super(props);

      this.state = {
        data: [],
        page: 1,
        refreshState: RefreshState.Idle
      };
    }

    componentDidMount() {
      this.onHeaderRefresh();
    }

    onHeaderRefresh = () => {
      this.setState({ refreshState: RefreshState.HeaderRefreshing, page: 1 });

      fetch(
        `https://cnodejs.org/api/v1/topics?page=${
          this.state.page
        }&tab=share&limit=10`
      )
        .then(response => response.json())
        .then(responseJson => {
          this.setState({
            data: responseJson.data,
            page: this.state.page + 1,
            refreshState: RefreshState.Idle
          });
        })
        .catch(error => {
          console.log(error);
        });
    };
    onFooterRefresh = () => {
      this.setState({ refreshState: RefreshState.FooterRefreshing });

      fetch(
        `https://cnodejs.org/api/v1/topics?page=${
          this.state.page
        }&tab=share&limit=10`
      )
        .then(response => response.json())
        .then(responseJson => {
          this.setState({
            data: [...this.state.data, ...responseJson.data],
            page: this.state.page + 1,
            refreshState: RefreshState.Idle
          });
        })
        .catch(error => {
          console.log(error);
        });
    };

    render() {
      return (
        <View style={styles.container}>
          <RefreshListView
            style={styles.container}
            data={this.state.data}
            keyExtractor={(item, index) => {
              index;
            }}
            renderItem={({ item }) => (
              <View style={{ flexDirection: "row", width: "100%", height: 80 }}>
                <Image
                  style={{ width: 60, height: 60, margin: 5 }}
                  source={{ uri: item.author.avatar_url }}
                />
                <Text
                  style={{ flex: 1, fontSize: 18 }}
                  onPress={() => {
                    this.props.navigation.navigate("Details", {
                      abc: item.content
                    });
                  }}
                >
                  {item.title}
                </Text>
              </View>
            )}
            refreshState={this.state.refreshState}
            onHeaderRefresh={this.onHeaderRefresh}
            onFooterRefresh={this.onFooterRefresh}
          />
        </View>
      );
    }
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: "#F5FCFF"
    }
  });
