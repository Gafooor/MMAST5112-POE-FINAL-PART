# MMAST5112-POE-FINAL-PART
CODE FOR ENTIRE APP:
import React, { useState } from "react";
import { Text, View, Button, FlatList, TextInput, TouchableOpacity, Alert, SafeAreaView, ScrollView } from "react-native";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";

const Stack = createStackNavigator();

// Initial Menu Data
const initialDishes = {
  starters: [
    { id: "1", name: "Salad", description: "Fresh garden salad", price: 5 },
    { id: "2", name: "Soup", description: "Tomato soup", price: 4 },
  ],
  mains: [
    { id: "3", name: "Steak", description: "Grilled steak", price: 15 },
    { id: "4", name: "Pasta", description: "Pasta with sauce", price: 12 },
  ],
  desserts: [
    { id: "5", name: "Ice Cream", description: "Vanilla ice cream", price: 6 },
    { id: "6", name: "Cake", description: "Chocolate cake", price: 7 },
  ],
  drinks: [
    { id: "7", name: "Water", description: "Mineral water", price: 2 },
    { id: "8", name: "Wine", description: "Red wine", price: 8 },
  ],
};

const App = () => {
  const [menuItems, setMenuItems] = useState(initialDishes);

  // Calculate average prices
  const calculateAveragePrice = (category) => {
    const items = menuItems[category];
    const total = items.reduce((sum, item) => sum + item.price, 0);
    return (total / items.length).toFixed(2);
  };

  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        {/* Home Screen */}
        <Stack.Screen name="Home">
          {({ navigation }) => (
            <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
              <Text>Average Prices:</Text>
              {["starters", "mains", "desserts", "drinks"].map((category) => (
                <Text key={category}>
                  {category.charAt(0).toUpperCase() + category.slice(1)}: R{calculateAveragePrice(category)}
                </Text>
              ))}
              <Button title="Client" onPress={() => navigation.navigate("ClientMenu")} />
              <Button title="Chef" onPress={() => navigation.navigate("EditMenu", { menuItems, setMenuItems })} />
            </View>
          )}
        </Stack.Screen>

        {/* Client Menu */}
        <Stack.Screen name="ClientMenu">
          {({ navigation }) => {
            const [filteredCategory, setFilteredCategory] = useState(null);

            const filterMenu = () => {
              if (!filteredCategory) {
                return Object.values(menuItems).flat();
              }
              return menuItems[filteredCategory];
            };

            return (
              <SafeAreaView style={{ flex: 1 }}>
                <Text>Select a Category:</Text>
                {["starters", "mains", "desserts", "drinks"].map((category) => (
                  <Button
                    key={category}
                    title={category}
                    onPress={() => setFilteredCategory(category)}
                  />
                ))}
                <FlatList
                  data={filterMenu()}
                  keyExtractor={(item) => item.id}
                  renderItem={({ item }) => (
                    <Text>
                      {item.name}: {item.description} - R{item.price}
                    </Text>
                  )}
                />
                <Button title="Back" onPress={() => navigation.goBack()} />
              </SafeAreaView>
            );
          }}
        </Stack.Screen>

        {/* Chef Menu Edit */}
        <Stack.Screen name="EditMenu">
          {({ navigation, route }) => {
            const { menuItems, setMenuItems } = route.params;
            const [newDish, setNewDish] = useState({
              name: "",
              description: "",
              price: "",
              category: "starters",
            });

            const addDish = () => {
              if (!newDish.name || !newDish.description || !newDish.price) {
                Alert.alert("Please fill all fields!");
                return;
              }

              setMenuItems((prev) => ({
                ...prev,
                [newDish.category]: [
                  ...prev[newDish.category],
                  {
                    id: Date.now().toString(),
                    ...newDish,
                    price: parseFloat(newDish.price),
                  },
                ],
              }));

              setNewDish({ name: "", description: "", price: "", category: "starters" });
            };

            const removeDish = (category, id) => {
              setMenuItems((prev) => ({
                ...prev,
                [category]: prev[category].filter((item) => item.id !== id),
              }));
            };

            return (
              <ScrollView style={{ flex: 1 }}>
                <Text>Edit Menu:</Text>
                {Object.keys(menuItems).map((category) => (
                  <View key={category}>
                    <Text>{category.toUpperCase()}:</Text>
                    {menuItems[category].map((item) => (
                      <View key={item.id} style={{ flexDirection: "row", alignItems: "center" }}>
                        <Text>
                          {item.name} - R{item.price}
                        </Text>
                        <Button title="Delete" onPress={() => removeDish(category, item.id)} />
                      </View>
                    ))}
                  </View>
                ))}
                <Text>Add New Dish:</Text>
                <TextInput
                  placeholder="Name"
                  value={newDish.name}
                  onChangeText={(text) => setNewDish((prev) => ({ ...prev, name: text }))}
                />
                <TextInput
                  placeholder="Description"
                  value={newDish.description}
                  onChangeText={(text) => setNewDish((prev) => ({ ...prev, description: text }))}
                />
                <TextInput
                  placeholder="Price"
                  keyboardType="numeric"
                  value={newDish.price}
                  onChangeText={(text) => setNewDish((prev) => ({ ...prev, price: text }))}
                />
                <Button title="Add Dish" onPress={addDish} />
                <Button title="Back to Home" onPress={() => navigation.goBack()} />
              </ScrollView>
            );
          }}
        </Stack.Screen>
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;
